#fork 官方的进行自定义
- 将完全版的镜像push到阿里云，每次部署不需要再进行镜像的制作
- 部署时直接从阿里云获取完全版镜像
- docker-compose.yml.orgin为原文件
- 改动：
    - 去除cron/sentry的Dockerfile中复制安装操作
    - docker-compose.yml中的镜像源更改为阿里云的仓库
    - .env中设置的SENTRY_IMAGE为阿里云仓库
- 统计：
    - 共用到了10个镜像
        - sentry
        - snuba
        - redis
        - memcached
        - symbolicator
        - postgres
        - stmp(tianon/exim4)
        - clickhouse-server
        - kafka
        - zookeeper
    - 生成18个服务，即会运行18个容器
        - snuba-api
        - snuba-consumer
        - snuba-replacer
        - snuba-cleanup
        - symbolicator-cleanup
        - sentry-web
        - sentry-cron
        - sentry-worker
        - sentry-post-process-forwarder
        - sentry-cleanup

        
# 官方：Sentry 10 On-Premise [![Build Status][build-status-image]][build-status-url]

Official bootstrap for running your own [Sentry](https://sentry.io/) with [Docker](https://www.docker.com/).

## Requirements

 * Docker 17.05.0+
 * Compose 1.23.0+

## Minimum Hardware Requirements:

 * You need at least 2400MB RAM

## Setup

To get started with all the defaults, simply clone the repo and run `./install.sh` in your local check-out.

There may need to be modifications to the included example config files (`sentry/config.example.yml` and `sentry/sentry.conf.example.py`) to accommodate your needs or your environment (such as adding GitHub credentials). If you want to perform these, do them before you run the install script and copy them without the `.example` extensions in the name (such as `sentry/sentry.conf.py`) before running the `install.sh` script.

The recommended way to customize your configuration is using the files below, in that order:

 * `config.yml`
 * `sentry.conf.py`
 * `.env` w/ environment variables

We currently support a very minimal set of environment variables to promote other means of configuration.

If you have any issues or questions, our [Community Forum](https://forum.sentry.io/c/on-premise) is at your service! Everytime you run the install script, it will generate a log file, `sentry_install_log-<ISO_TIMESTAMP>.txt` with the output. Sharing these logs would help people diagnose any issues you might be having.

## Versioning

We continously push the Docker image for each commit made into [Sentry](https://github.com/getsentry/sentry), and other services such as [Snuba](https://github.com/getsentry/snuba) or [Symbolicator](https://github.com/getsentry/symbolicator) to [our Docker Hub](https://hub.docker.com/u/getsentry) and tag the latest version on master as `:latest`. This is also usually what we have on sentry.io and what the install script uses. You can use a custom Sentry image, such as a modified version that you have built on your own, or simply a specific commit hash by setting the `SENTRY_IMAGE` environment variable to that image name before running `./install.sh`:

```shell
SENTRY_IMAGE=getsentry/sentry:10 ./install.sh
```

or

```shell
SENTRY_IMAGE=getsentry/sentry:83b1380 ./install.sh
```

If you want to use different or specific images for other services, you may create a `docker-compose.overrides.yaml` file in the repo and override the `image` field for the corresponding services.

We strongly recommend keeping the `latest` tags for all, if you are using this repository directly. We also recommend using specific commit tags if you are consuming any of our Docker images in an environment that needs consistent deploys such as [a Helm chart](https://github.com/helm/charts/tree/master/stable/sentry).

## Event Retention

Sentry comes with a cleanup cron job that prunes events older than `90 days` by default. If you want to change that, you can change the `SENTRY_EVENT_RETENTION_DAYS` environment variable in `.env` or simply override it in your environment. If you do not want the cleanup cron, you can remove the `sentry-cleanup` service from the `docker-compose.yml`file.

## Securing Sentry with SSL/TLS

If you'd like to protect your Sentry install with SSL/TLS, there are
fantastic SSL/TLS proxies like [HAProxy](http://www.haproxy.org/)
and [Nginx](http://nginx.org/). You'll likely want to add this service to your `docker-compose.yml` file.

## Updating Sentry

_You need to be on at least Sentry 9.1.2 to be able to upgrade automatically to the latest version. If you are not, upgrade to 9.1.2 first by checking out the [9.1.2 tag](https://github.com/getsentry/onpremise/tree/9.1.2) on this repo._

The included `install.sh` script is meant to be idempotent and to bring you to the latest version. What this means is you can and should run `install.sh` to upgrade to the latest version available. Remember that the output of the script will be stored in a log file, `sentry_install_log-<ISO_TIMESTAMP>.txt`, which you may share for diagnosis if anything goes wrong.

## Resources

 * [Documentation](https://docs.sentry.io/server/)
 * [Bug Tracker](https://github.com/getsentry/onpremise/issues)
 * [Forums](https://forum.sentry.io/c/on-premise)
 * [Discord](https://discord.gg/mg5V76F) (Sentry Community, #sentry-server)


[build-status-image]: https://api.travis-ci.com/getsentry/onpremise.svg?branch=master
[build-status-url]: https://travis-ci.com/getsentry/onpremise
