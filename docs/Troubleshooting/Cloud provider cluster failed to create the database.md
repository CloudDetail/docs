Some cloud service providers' Persistent Volume (PV) auto-creation tools do not allow components to execute `chmod` and `chown` commands within the Persistent Volume, leading to the failure of database component startup.

**Solution Approaches:**

1. Grant the pod operation permissions to the Persistent Volume path folder on the host machine.
2. Configure the database to skip the execution of `chmod` and `chown` during initialization. For instance, Clickhouse can bypass these commands by setting the environment variable `CLICKHOUSE_DO_NOT_CHOWN=1`.
3. Host the database according to the [《Advanced》](/docs/Installation/Advanced/).
