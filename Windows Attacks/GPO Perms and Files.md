- **Group Policy Object (GPO)** is a virtual collection of policy settings that has a unique name
- Each GPO contains a collection of zero or more policy settings. They are linked to an `Organizational Unit` in the AD structure
- By default only Domain admins (and similar privileged roles) can modify it, but not always the case
- Modifications on a GPO can include additions of start-up scripts or a scheduled task to execute a file
-------------------
## Detection:

- when a GPO is modified. If Directory Service Changes auditing is enabled, then the event ID (`5136`) will be generated