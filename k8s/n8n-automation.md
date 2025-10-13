# n8n

See available tags on DockerHub [https://hub.docker.com/r/n8nio/n8n/tags?name=1.109](https://hub.docker.com/r/n8nio/n8n/tags?name=1.109)

## Issues

Current issues logs when starting up:
```bash
There are deprecations related to your environment variables. Please take the recommended actions to update your configuration:
 - DB_SQLITE_POOL_SIZE -> Running SQLite without a pool of read connections is deprecated. Please set `DB_SQLITE_POOL_SIZE` to a value higher than zero. See: https://docs.n8n.io/hosting/configuration/environment-variables/database/#sqlite
 - N8N_RUNNERS_ENABLED -> Running n8n without task runners is deprecated. Task runners will be turned on by default in a future version. Please set `N8N_RUNNERS_ENABLED=true` to enable task runners now and avoid potential issues in the future. Learn more: https://docs.n8n.io/hosting/configuration/task-runners/
 - N8N_BLOCK_ENV_ACCESS_IN_NODE -> The default value of N8N_BLOCK_ENV_ACCESS_IN_NODE will be changed from false to true in a future version. If you need to access environment variables from the Code Node or from expressions, please set N8N_BLOCK_ENV_ACCESS_IN_NODE=false. Learn more: https://docs.n8n.io/hosting/configuration/environment-variables/security/
```
