# hello

矩云科技基础工程师培训 · 第六课 / 第七课的示例应用。

一个最小的 FastAPI 服务，用来演示「代码 → 镜像 → 仓库 → 集群」这条完整流水线。

## 接口

| 路径 | 返回 |
|---|---|
| `/` | `{"hello": "world v2"}` |
| `/health` | `{"status": "ok"}` |

## 本地怎么跑

```bash
docker build --platform linux/amd64 -t hello:v1 .
docker run -d -p 8000:8000 --name hello hello:v1
curl localhost:8000/health
```

> `--platform linux/amd64` 是给 Apple 芯片用的。集群节点是 amd64，不加的话本地能跑、推上去跑不了。

## 镜像怎么构建的

**本仓库不用你在本地编译。** 每次 `git push` 到 `main` 分支，GitHub Actions 会自动：

1. 检出代码
2. 登录 `registry.dev.oaiai.ai`
3. 构建镜像并推送，打上两个 tag：
   - `gh<运行序号>` —— 每次构建一个，方便追溯
   - `latest` —— 始终指向最新一次

工作流定义在 [`.github/workflows/build.yml`](.github/workflows/build.yml)。

GitHub 的 runner 本身就是 amd64，所以**不需要** `--platform` 参数——这正是用它编译的好处之一。

### 需要配置的 Secrets

仓库 Settings → Secrets and variables → Actions 里需要两个：

| 名称 | 值 |
|---|---|
| `REGISTRY_USERNAME` | `student` |
| `REGISTRY_PASSWORD` | 教学镜像仓库的口令 |

## 部署到集群

镜像构建完成后，在集群上把 Deployment 指向新 tag 即可：

```bash
kubectl -n st-heyang set image deploy/hello \
  web=registry.dev.oaiai.ai/student/heyang-hello:gh1
kubectl -n st-heyang rollout status deploy/hello
```

回滚：

```bash
kubectl -n st-heyang rollout undo deploy/hello
```
