# Laravel Octane on Swoole

This Docker container runs Laravel on Swoole, using [Laravel Octane](https://github.com/laravel/octane). This container is built nightly from the latest version of Laravel and pushed to the [Docker registry](https://hub.docker.com/r/anterisdev/laravel-swoole).

## Usage

To quickly get up and running, you can use the following command to run the container. This will expose the Laravel application at [`http://localhost:8000`](http://localhost:8000).

> **Note:** It is important to pass the environment variable `APP_KEY` to the container. For more information, see [here](https://laravel.com/docs/8.x/encryption#configuration).

```bash
docker run -it \
    -p 8000:8000 \
    -e "APP_KEY=base64:1RfxZT785MHjMkAsIouOaukQHk77Ov+/0Y95EfHmhA8=" \
    anterisdev/laravel-swoole:latest
```

If you would like to run your own Laravel application in this container, be sure to check out the [volumes](#volumes) section below.

## Ports

Laravel Octane runs Swoole on port `8000`. This is the only port that is currently exposed by the container.

## Environment Variables

Several environment variables expose configuration options for Swoole. These are listed below.

| Name | Value | Description |
|---|---|---|
| SWOOLE_MAX_REQUESTS | `int` | How many requests Swoole workers should handle before being gracefully restarted. For more information, see [here](https://laravel.com/docs/8.x/octane#specifying-the-max-request-count).
| SWOOLE_TASK_WORKERS | `int` or `auto` | The number of task workers that should be created to handle concurrent tasks. For more information, see [here](https://laravel.com/docs/8.x/octane#specifying-the-worker-count).
| SWOOLE_WATCH | `yes` | If this environment variable is passed, Swoole will be run in watch mode so that it auto-reloads changes to your application. This is something you will want to run during development. For more information, see [here](https://laravel.com/docs/8.x/octane#watching-for-file-changes).
| SWOOLE_WORKERS | `int` or `auto` | The number of workers that should be created to handle requests. For more information, see [here](https://laravel.com/docs/8.x/octane#specifying-the-worker-count).

## Volumes

You are probably looking to run your own Laravel application in this Docker container. To do so, you will need to inject your application code. The easiest way to do this is with a volume.

> **Note**: There are a few prerequisites for this step. Make sure you have installed Laravel Octane in your  application. By running:
> ```bash
> composer require laravel/octane
> php artisan install:octane --server="swoole"
> ```
> For more information, see [here](https://laravel.com/docs/8.x/octane#installation).

The Laravel application files are stored at `/srv/laravel` in the Docker container. You will need to [mount](https://docs.docker.com/storage/volumes/) your application to that directory. A basic example of this can be seen below. Notice that we also added the `-e "SWOOLE_WATCH=yes"` option so that Swoole goes into watch mode.

```bash
docker run -it \
    -p 8000:8000 \
    -e "APP_KEY=base64:1RfxZT785MHjMkAsIouOaukQHk77Ov+/0Y95EfHmhA8=" \
    -e "SWOOLE_WATCH=yes" \
    --mount type=bind,source="$(pwd)"/my-laravel-app,target=/srv/laravel \
    anterisdev/laravel-swoole:latest
```
