# zhiying-meta

`zhiying-tutor` 项目的伞型 meta-repo。本身只承载跨仓库的共享内容，**不**包含可构建代码。

## 包含

- [`AGENTS.md`](./AGENTS.md) — 跨仓库的协作约定（子仓库各自有独立 `AGENTS.md`，进入子目录自动加载）。
- [`ARCHITECTURE.md`](./ARCHITECTURE.md) — 跨仓库的技术全景（分层、状态机、解锁链、异步生成流、数据模型）。
- [`justfile`](./justfile) — 引入全部子模块的 `just` 入口；提供 `bootstrap` / `sync` / `up` / `down` / `ps`。

## 子仓库

通过 `just bootstrap` 平级 clone 到本目录，各自独立 git 历史：

| 目录 | 角色 |
|---|---|
| `zhiying-infra/` | 共享中间件（PostgreSQL + RabbitMQ）的 docker compose |
| `zhiying-backend/` | 主后端（Rust + Axum + SeaORM） |
| `zhiying-mocks/` | 7 个微服务的 mock（Python + uv） |
| `zhiying-frontend/` | Web 前端（Next.js + pnpm） |
| `zhiying-ui/` | 静态 HTML 视觉/交互原型 |

`just up` 后会有更多由队友维护的真实微服务接入（视频生成等）；它们自己在 GitHub 组织下有独立 repo，本地联调时可手动 clone 或扩到 `justfile` 的 `sub_repos` 里。

## 起步

```bash
git clone git@github.com:zhiying-tutor/zhiying-meta.git zhiying-tutor
cd zhiying-tutor
just bootstrap          # clone 5 个子仓库
just up                 # 拉起整套本地栈
just ps                 # 查看正在运行的 tmux session 与 compose 服务
just down               # 反序停掉
just sync               # 对所有子仓库 git pull --ff-only
just status             # 跨仓库 short status 速览
```

## 子仓库快捷调用

`just <repo> <recipe>` 透传到对应子仓库，例如：

```bash
just zhiying-backend serve     # cargo run，挂在 tmux session zhiying-backend
just zhiying-backend log       # tmux attach 进去看日志，C-b d 脱出
just zhiying-backend stop
just zhiying-mocks serve       # uv run zhiying-mocks
just zhiying-frontend typecheck
just zhiying-infra log         # docker compose logs -f
```

完整列表用 `just --list <repo>`。

## 不做的事

- meta-repo 不锁子仓库版本（不用 submodule）。各子仓库独立 release / 独立 PR。
- meta-repo 不放业务代码、不放任何编译产物。
- 队友自治的微服务 repo 不强制纳入；需要联调时本地 clone 即可。
