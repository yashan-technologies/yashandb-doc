Data access control refers to the measures taken to prevent unauthorized users from accessing data in the database, and it is an important part of the YashanDB security system.

YashanDB supports discretionary access control, RBAC, and mandatory access control:

- Discretionary Access Control: The owner of the database object decides who has the right to access that object, allowing users to manage their own data access privilege.

- RBAC: The privileges that a user has are determined by the roles granted to the user. Administrators can manage the privileges of corresponding users in bulk by managing the privilege set of roles. RBAC can also categorize the privileges and roles of database administrators to build an administrator privilege system with "Separation of Duties."

- Mandatory Access Control: The system enforces control over user access privileges to specific data according to predetermined security policies. YashanDB implements mandatory access control using LBAC.