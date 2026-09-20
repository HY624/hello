FROM nginx:alpine

# 只换掉默认首页，其余全用 nginx 官方镜像自带的。
# 页面内容进镜像、不进 ConfigMap —— 这样「改一行字」就是一次真正的发布：
# 新版镜像 → 新 tag → set image → 滚动更新。
COPY index.html /usr/share/nginx/html/index.html
