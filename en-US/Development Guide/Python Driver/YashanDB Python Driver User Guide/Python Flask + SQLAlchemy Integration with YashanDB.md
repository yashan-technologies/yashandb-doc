This document describes how to integrate YashanDB yaspy driver in Python Flask + SQLAlchemy projects and explains the connection pool configuration parameters in detail.

> **Note**：
>
> SQLAlchemy integration in this document uses the `yashandb+yaspy` URI format to connect to YashanDB. The `yaspy` driver needs to be used together with the `yashandb-sqlalchemy` package. Installing the `yaspy` package alone cannot directly use the `yaspy://` URI. Please refer to the official integration documentation for details.

## Prerequisites

1. Install Python environment.

​	Ensure Python 3.6 or later is installed. You can check the Python version with the following command:

```bash
python --version
```

2. Install YashanDB Python driver.

​	Install yaspy driver and yashandb-sqlalchemy dialect using pip:

```bash
pip install yaspy yashandb-sqlalchemy
```

    Or download the corresponding version wheel package from YashanDB official website for installation:

```bash
pip install yashandb-sqlalchemy
pip install yaspy-1.0.2-cp312-cp312-win_amd64.whl
```

3. Install Flask and SQLAlchemy.

```bash
pip install flask flask-sqlalchemy
```

## Project Configuration

### Project Structure

```python
yasdb_demo/
├── app.py                 # Application entry
├── config.py              # Configuration file
├── models.py              # Data model
├── routes.py              # Route handling
├── requirements.txt       # Dependency list
└── README.md              # Project readme
```

### Configuration File

Create config.py configuration file:

```python
# Configuration file

class Config:
    """Application Configuration"""

    # YashanDB database connection configuration
    DB_HOST = "192.168.1.2"
    DB_PORT = 1688
    DB_USER = "your_username"
    DB_PASSWORD = "your_password"
    DB_NAME = "yasdb"

    # DSN format connection (yashandb+yaspy is the SQLAlchemy dialect identifier)
    SQLALCHEMY_DATABASE_URI = f"yashandb+yaspy://{DB_USER}:{DB_PASSWORD}@{DB_HOST}:{DB_PORT}/{DB_NAME}"

    # Connection Pool Configuration
    SQLALCHEMY_ENGINE_OPTIONS = {
        # Basic Configuration
        'pool_size': 10,           # Connection pool size
        'pool_recycle': 3600,      # Connection recycle time in seconds
        'pool_pre_ping': True,     # Test connection before use
        'max_overflow': 5,         # Maximum overflow connections
    }

    # Other Configuration
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SECRET_KEY = 'your-secret-key'
```

### Create Application

Create app.py application entry file:

```python
# Application entry
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from config import Config
from models import Base

# Create Flask application
app = Flask(__name__)
app.config.from_object(Config)

# Initialize database
db = SQLAlchemy(app)

# Create database tables
with app.app_context():
    Base.metadata.create_all(db.engine)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

## Code Implementation
### Data Model

Create models.py file to define data models:

```python
# Data Model
from sqlalchemy import Column, DateTime, Integer, String, func
from sqlalchemy.orm import declarative_base

# Create declarative base class
Base = declarative_base()

class User(Base):
    """User Model"""

    # Table name
    __tablename__ = 'users'

    # Field definition
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(50), unique=True, nullable=False, index=True)
    email = Column(String(100), nullable=False)
    phone = Column(String(20))
    create_time = Column(DateTime, server_default=func.current_timestamp())

    def to_dict(self):
        """Convert to dictionary"""
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

### Route Handling

Create routes.py file to handle API requests:

```python
# Route Handling
from flask import Blueprint, request, jsonify
from sqlalchemy import select
from flask import current_app
from models import User

# Create blueprint
api_bp = Blueprint('api', __name__, url_prefix='/api/users')

def get_db():
    """Get the db instance of the current application"""
    return current_app.extensions['sqlalchemy'].db

# Get all users
@api_bp.route('', methods=['GET'])
def get_all_users():
    """Get all user list"""
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

# Get single user
@api_bp.route('/<int:user_id>', methods=['GET'])
def get_user(user_id):
    """Get user by ID"""
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

# Create user
@api_bp.route('', methods=['POST'])
def create_user():
    """Create new user"""
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

# Update user
@api_bp.route('/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    """Update user information"""
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

# Delete user
@api_bp.route('/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    """Delete user"""
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

### Update Application Entry

Update app.py to register blueprint:

```python
# Application entry
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from config import Config
from models import Base

# Create Flask application
app = Flask(__name__)
app.config.from_object(Config)

# Initialize database
db = SQLAlchemy(app)

# Register blueprint
from routes import api_bp
app.register_blueprint(api_bp)

# Create database tables
with app.app_context():
    db.create_all()

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

## Connection Pool Configuration Parameters
### SQLAlchemy Connection Pool Parameters

|Parameter Name |Parameter Description |Example Value |
| ------------------------- | ------------------------------------------------------------ | ---------------------- |
| pool_size | Number of connections maintained in the connection pool | 10 |
| pool_recycle | Connection recycle time in seconds, connections older than this will be recreated | 3600 |
| pool_pre_ping | Test connection validity before acquiring from pool | True |
| max_overflow | Maximum number of overflow connections allowed when pool is full | 5 |

### Connection String Format

```python
yashandb+yaspy://username:password@host:port/database
```

Multi-address connection format is supported:

```python
# Primary-standby type (default)
yashandb+yaspy://username:password@host1:port,host2:port/database

# Load balance type
yashandb+yaspy://username:password@LOADBALANCE:host1:port,host2:port/database
```

## Testing and Verification
### Start Application

```bash
python app.py
```

### API Testing

Use curl or Postman to test API:

```bash
# Query all users
curl http://localhost:5000/api/users

# Query user by ID
curl http://localhost:5000/api/users/1

# Create user
curl -X POST http://localhost:5000/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","phone":"13800138000"}'

# Update user
curl -X PUT http://localhost:5000/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"username":"updateduser","email":"updated@example.com","phone":"13900139000"}'

# Delete user
curl -X DELETE http://localhost:5000/api/users/1
```

## Common Issues

### What should I do if connection times out?

- Solution: Check if the database service is running properly.

### What should I do if the connection pool is exhausted?

- Solution: Increase pool_size and max_overflow values.

### What should I do if authentication fails?

- Solution: Check DB_USER and DB_PASSWORD in config.py.

### What should I do if module import error occurs?

- Solution: Ensure yaspy driver and yashandb-sqlalchemy package are installed correctly, run `pip install yaspy yashandb-sqlalchemy`.

## Performance Optimization Recommendations

### Connection Pool Tuning

- Adjust pool_size and max_overflow parameters based on application concurrency.
- In multi-address connection scenarios, configure `pool_timeout` parameter via DSN to control timeout.
- Enable pool_pre_ping to detect invalid connections.

### SQL Optimization

- Use indexes to optimize query performance.
- Avoid using select *, only query required fields.
- Use batch operations to improve insert/update efficiency.

### Monitoring and Logging

- Enable SQLAlchemy logs to monitor query execution.
- Regularly check slow query logs.
- Monitor application performance metrics.
