# Docker Services

通过 Docker Compose 管理多个服务的部署。

## 项目结构

```
.
├── shared/                   # 共享配置
│   └── networks.yml          # 网络定义（可被多个主机复用）
├── hosts/                    # 按主机组织，每个主机有自己的总入口
│   └── <hostname>/
│       ├── compose.yml
│       ├── .env.example      # 集中管理该主机所有服务的环境变量
│       ├── .env.local        # 本地运行配置（不提交 git）
│       ├── config/           # 集中存放所有服务的配置文件
│       │   └── <service-name>/
│       └── data/             # 集中存放所有服务的本地数据
│           └── <service-name>/
├── services/                 # 服务定义，可被多个主机复用
│   └── <service-name>/
│       └── compose.yml
└── .gitignore
```

## 使用方法

1. 进入目标主机目录：
   ```bash
   cd hosts/<hostname>
   ```

2. 复制环境变量模板并填写（二选一）：
   - `.env`：Docker Compose 自动加载，适合非敏感配置
   - `.env.local`：需手动指定，适合敏感或本地覆盖配置
   ```bash
   cp .env.example .env        # 方式一：自动加载
   # 或
   cp .env.example .env.local  # 方式二：手动指定
   ```

3. 启动服务：
   ```bash
   docker compose up -d                      # 使用 .env
   # 或
   docker compose --env-file .env.local up -d # 使用 .env.local
   ```

## 添加新服务

1. 在 `services/` 下创建服务目录及 `compose.yml`
2. 在需要该服务的 `hosts/<hostname>/compose.yml` 中添加 `include`
3. 将该服务需要的环境变量（包括 `DATA_DIR`、`CONFIG_DIR`）添加到对应主机的 `.env.example` 中
4. 创建目录 `hosts/<hostname>/data/<service-name>/` 和 `hosts/<hostname>/config/<service-name>/`

## 添加新主机

1. 在 `hosts/` 下创建主机目录
2. 创建 `compose.yml`，`include` 该主机需要的服务
3. 创建 `.env.example`，包含所有引用服务的环境变量
