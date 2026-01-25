---
name: personal:mac-docker
description: 个人 Docker 服务管理配置。包含服务清单、路径、端口映射。触发词：docker、容器、heimdall、portainer、qinglong
---

# Mac Docker 服务管理

> **个人配置示例**：路径和服务列表需根据实际环境修改。

管理本机 Docker 容器化服务，包括启停、日志查看、状态检查。

## 配置说明

使用前请修改以下路径为你的实际路径：

```
BASE_PATH=~/docker
```

## 服务目录

| 目录 | 说明 |
|------|------|
| `$BASE_PATH/docker-compose/` | docker-compose 文件 |
| `$BASE_PATH/envs/` | 环境变量配置 |
| `$BASE_PATH/appdata/` | 持久化数据卷 |
| `$BASE_PATH/appdirectory/` | 额外服务数据 |

## 服务清单

| 服务 | compose 文件 | 端口 | 说明 |
|------|-------------|------|------|
| Heimdall | heimdall-compose.yml | 5233, 5234 | 仪表盘 |
| Nginx PM | nginx-pm-compose.yml | 80, 81, 443 | 反向代理 |
| Portainer | portainer-compose.yml | 9000 | Docker 管理 |
| Qinglong | qinglong-compose.yml | 5700 | 定时任务 |
| MySQL | mysql-compose.yml | 3306 | 数据库 |
| Mermaid | mermaid-compose.yml | 3301 | 图表渲染 |
| Subconverter | subconverter-compose.yml | 25500 | 订阅转换 |
| Sub-web | sub-web-compose.yml | 18080 | 订阅前端 |

## 常用操作

```bash
# 查看所有容器
docker ps -a --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# 启动服务
docker compose -f $BASE_PATH/docker-compose/[service]-compose.yml up -d

# 停止服务
docker compose -f $BASE_PATH/docker-compose/[service]-compose.yml down

# 重启服务
docker compose -f $BASE_PATH/docker-compose/[service]-compose.yml restart

# 查看日志
docker logs [container-name] --tail 100
```

## 访问地址

| 服务 | 地址 |
|------|------|
| Heimdall | http://localhost:5233 |
| Nginx PM | http://localhost:81 |
| Portainer | http://localhost:9000 |
| Qinglong | http://localhost:5700 |
| Mermaid | http://localhost:3301 |
| Sub-web | http://localhost:18080 |

## 数据备份

重要目录：
- `$BASE_PATH/appdata/`
- `$BASE_PATH/appdirectory/`
