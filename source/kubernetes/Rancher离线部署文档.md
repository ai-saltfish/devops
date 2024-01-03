# 离线rancher部署

## 1 前置步骤

```bash
# 官网下载脚本
rancher-image.txt
rancher-save-image.sh
rancher-load-image.sh
```

## 2 部署docker镜像仓库

### 3 部署rancher

```bash
# 下载镜像
./rancher-save-image.sh --image-list rnahcer-image.txt

# 上传镜像到本地仓库
./rancher-load-image.sh --image-list rancher-image.txt  registry=<registry_name:port>

# docker方式部署离线rancher
docker run -d --restart=unless-stopped \
    -p 80:80 -p 443:443 \
    -e CATTLE_SYSTEM_DEFAULT_REGISTRY=<REGISTRY.YOURDOMAIN.COM:PORT> \ # 设置在 Rancher 中使用的默认私有镜像仓库
    -e CATTLE_SYSTEM_CATALOG=bundled \ # 使用打包的 Rancher System Chart
    --privileged \
    <REGISTRY.YOURDOMAIN.COM:PORT>/rancher/rancher:<RANCHER_VERSION_TAG>
    
# 如果镜像仓库未使用tls，那么rancher启动时需要挂载镜像文件
-v <path>/registries.yaml:/etc/rancher/k3s/registries.yaml

# registries.yaml文件内容
mirrors:
  "<registry_name:port>":
    endpoint:
      "http://<registry_name:port>"
```

