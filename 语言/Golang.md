## 文档

[Go语言中文网](https://books.studygolang.com/)

## 配置

### Proxy镜像

```bash
go env -w  GOPROXY=https://goproxy.cn,direct
```

## 依赖管理

### Mod

- [使用](https://juejin.im/post/5c8e503a6fb9a070d878184a)
- [常见命令及问题](https://blog.csdn.net/zzhongcy/article/details/97243826)

## Gin

[tutorial](https://youngxhui.top/categories/gin/)

## swagger

[使用与gin集成](https://ieevee.com/tech/2018/04/19/go-swag.html)

## 问题

### 安装swagger报错：missing dot in first path element

goroot设置有问题，不要使用软链接，使用go的真实安装目录

### docker container to wait for MySQL container

[app容器等待mysql容器先启动后再启动](http://www.inanzzz.com/index.php/post/jma5/forcing-go-docker-container-to-wait-for-mysql-container)
