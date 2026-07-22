本文档介绍如何在Python Flask + SQLAlchemy项目中集成YashanDB，通过yashandb-sqlalchemy dialect实现与YashanDB的连接，并详细说明连接池的配置参数。

> **Note**：
>
> SQLAlchemy集成本文档以`yashandb+yaspy`URI格式连接YashanDB。`yaspy`驱动需配合`yashandb-sqlalchemy`包使用，仅安装`yaspy`包无法直接使用`yaspy://`URI。具体请参考官方集成文档。

## 准备工作

1. 安装Python环境。

​	确保已安装Python 3.6及以上版本。可以通过以下命令检查Python版本：

```bash
python --version
```

2. 安装YashanDB Python驱动。

​	使用pip安装yaspy驱动：

```bash
pip install yaspy yashandb-sqlalchemy
```

    或者从YashanDB官网下载对应版本的wheel包进行安装：

```bash
pip install yashandb-sqlalchemy
pip install yaspy-1.0.2-cp312-cp312-win_amd64.whl
```

3. 安装Flask和SQLAlchemy。

```bash
pip install flask flask-sqlalchemy
```

## 项目配置

### 项目结构

```python
yasdb_demo/
├── app.py                 # Application entry
├── config.py              # Configuration file
├── models.py              # Data model
├── routes.py              # Route handling
├── requirements.txt       # Dependency list
└── README.md              # Project readme
```

### 配置文件

创建config.py配置文件：

```python
# 配置文件

class Config:
    """应用配置"""

    # YashanDB数据库连接配置
    DB_HOST = "192.168.1.2"
    DB_PORT = 1688
    DB_USER = "your_username"
    DB_PASSWORD = "your_password"
    DB_NAME = "yasdb"

    # DSN格式连接（yashandb+yaspy为SQLAlchemy dialect标识符）
    SQLALCHEMY_DATABASE_URI = f"yashandb+yaspy://{DB_USER}:{DB_PASSWORD}@{DB_HOST}:{DB_PORT}/{DB_NAME}"

    # 连接池配置
    SQLALCHEMY_ENGINE_OPTIONS = {
        # 基础配置
        'pool_size': 10,           # 连接池大小
        'pool_recycle': 3600,      # 连接回收时间（秒）
        'pool_pre_ping': True,     # 连接使用前检查
        'max_overflow': 5,         # 最大溢出连接数
    }

    # 其他配置
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SECRET_KEY = 'your-secret-key'
```

### 创建应用

创建app.py应用入口文件：

```python
# 应用入口
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from config import Config
from models import Base

# 创建Flask应用
app = Flask(__name__)
app.config.from_object(Config)

# 初始化数据库
db = SQLAlchemy(app)

# 创建数据库表
with app.app_context():
    Base.metadata.create_all(db.engine)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

## 代码实现
### 数据模型

创建models.py文件定义数据模型：

```python
# 数据模型
from sqlalchemy import Column, DateTime, Integer, String, func
from sqlalchemy.orm import declarative_base

# 创建声明性基类
Base = declarative_base()

class User(Base):
    """用户模型"""

    # 表名
    __tablename__ = 'users'

    # 字段定义
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(50), unique=True, nullable=False, index=True)
    email = Column(String(100), nullable=False)
    phone = Column(String(20))
    create_time = Column(DateTime, server_default=func.current_timestamp())

    def to_dict(self):
        """转换为字典"""
        return {
            'id': self.id,
            'username': self.username,
            'email': self.email,
            'phone': self.phone,
            'create_time': self.create_time.isoformat() if self.create_time else None
        }

    def __repr__(self):
        return f'<User {self.username}>'
```

### 路由处理

创建routes.py文件处理API请求：

```python
# 路由处理
from flask import Blueprint, request, jsonify
from sqlalchemy import select
from flask import current_app
from models import User

# 创建蓝图
api_bp = Blueprint('api', __name__, url_prefix='/api/users')

def get_db():
    """获取当前应用的db实例"""
    return current_app.extensions['sqlalchemy'].db

# 获取所有用户
@api_bp.route('', methods=['GET'])
def get_all_users():
    """获取所有用户列表"""
    try:
        db = get_db()
        stmt = select(User).order_by(User.id)
        users = db.session.execute(stmt).scalars().all()
        return jsonify({
            'code': 200,
            'message': 'success',
            'data': [user.to_dict() for user in users]
        })
    except Exception as e:
        return jsonify({
            'code': 500,
            'message': str(e),
            'data': []
        }), 500

# 获取单个用户
@api_bp.route('/<int:user_id>', methods=['GET'])
def get_user(user_id):
    """根据ID获取用户"""
    try:
        db = get_db()
        user = db.session.get(User, user_id)
        if user:
            return jsonify({
                'code': 200,
                'message': 'success',
                'data': user.to_dict()
            })
        return jsonify({
            'code': 404,
            'message': 'User not found',
            'data': None
        }), 404
    except Exception as e:
        return jsonify({
            'code': 500,
            'message': str(e),
            'data': None
        }), 500

# 创建用户
@api_bp.route('', methods=['POST'])
def create_user():
    """创建新用户"""
    try:
        db = get_db()
        data = request.get_json()
        user = User(
            username=data.get('username'),
            email=data.get('email'),
            phone=data.get('phone')
        )
        db.session.add(user)
        db.session.commit()
        return jsonify({
            'code': 200,
            'message': 'User created successfully',
            'data': user.to_dict()
        }), 201
    except Exception as e:
        db.session.rollback()
        return jsonify({
            'code': 500,
            'message': str(e),
            'data': None
        }), 500

# 更新用户
@api_bp.route('/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    """更新用户信息"""
    try:
        db = get_db()
        user = db.session.get(User, user_id)
        if not user:
            return jsonify({
                'code': 404,
                'message': 'User not found',
                'data': None
            }), 404

        data = request.get_json()
        user.username = data.get('username', user.username)
        user.email = data.get('email', user.email)
        user.phone = data.get('phone', user.phone)

        db.session.commit()
        return jsonify({
            'code': 200,
            'message': 'User updated successfully',
            'data': user.to_dict()
        })
    except Exception as e:
        db.session.rollback()
        return jsonify({
            'code': 500,
            'message': str(e),
            'data': None
        }), 500

# 删除用户
@api_bp.route('/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    """删除用户"""
    try:
        db = get_db()
        user = db.session.get(User, user_id)
        if not user:
            return jsonify({
                'code': 404,
                'message': 'User not found',
                'data': None
            }), 404

        db.session.delete(user)
        db.session.commit()
        return jsonify({
            'code': 200,
            'message': 'User deleted successfully',
            'data': None
        })
    except Exception as e:
        db.session.rollback()
        return jsonify({
            'code': 500,
            'message': str(e),
            'data': None
        }), 500
```

### 更新应用入口

更新app.py注册蓝图：

```python
# 应用入口
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from config import Config
from models import Base

# 创建Flask应用
app = Flask(__name__)
app.config.from_object(Config)

# 初始化数据库
db = SQLAlchemy(app)

# 注册蓝图
from routes import api_bp
app.register_blueprint(api_bp)

# 创建数据库表
with app.app_context():
    Base.metadata.create_all(db.engine)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

## 连接池配置参数详解
### SQLAlchemy连接池参数

| 参数名| 参数说明| 示例值|
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| pool_size | 连接池中保持的连接数量 | 10 |
| pool_recycle | 连接回收时间（秒），超过此时间的连接会被重新创建 | 3600 |
| pool_pre_ping | 每次从池中获取连接时，先测试连接是否有效 | True |
| max_overflow | 连接池满时允许创建的最大溢出连接数 | 5 |

### 连接字符串格式

```python
yashandb+yaspy://username:password@host:port/database
```

支持多地址连接格式：

```python
# 主备类型（默认）
yashandb+yaspy://username:password@host1:port,host2:port/database

# 负载均衡类型
yashandb+yaspy://username:password@LOADBALANCE:host1:port,host2:port/database
```

## 测试验证
### 启动应用

```bash
python app.py
```

### API测试

使用curl或Postman测试API：

```bash
# 查询所有用户
curl http://localhost:5000/api/users

# 根据ID查询用户
curl http://localhost:5000/api/users/1

# 创建用户
curl -X POST http://localhost:5000/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","phone":"13800138000"}'

# 更新用户
curl -X PUT http://localhost:5000/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"username":"updateduser","email":"updated@example.com","phone":"13900139000"}'

# 删除用户
curl -X DELETE http://localhost:5000/api/users/1
```

## 常见问题

### 连接超时怎么办？

- 解决方案：检查数据库服务是否正常运行。

### 连接池耗尽怎么办？

- 解决方案：增加pool_size和max_overflow值。

### 认证失败怎么办？

- 解决方案：检查config.py中的DB_USER和DB_PASSWORD配置。

### 模块导入错误怎么办？

- 解决方案：确保已正确安装yaspy驱动和yashandb-sqlalchemy包，`pip install yaspy yashandb-sqlalchemy`。

## 性能优化建议

### 连接池调优

- 根据应用并发量调整pool_size和max_overflow参数。
- 多地址连接场景下可通过DSN配置`pool_timeout`参数控制超时。
- 启用pool_pre_ping检测失效连接。

### SQL优化

- 使用索引优化查询性能。
- 避免使用select *，只查询需要的字段。
- 使用批量操作提高插入/更新效率。

### 监控与日志

- 启用SQLAlchemy日志监控查询执行。
- 定期检查慢查询日志。
- 监控应用性能指标。
