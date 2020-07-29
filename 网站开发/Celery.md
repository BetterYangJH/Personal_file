# Celery

处理大量消息的分布式系统，实时处理的任务队列，支持任务调度

在线安装

```shell
sudo pip3 install -U Celery
```

框架

```python
1 Task -异步任务
2 broker -消息传输的中间件，接收生产者消息[redis/rabbitmq]
3 worker -工作者 - 消费/执行broker中消息/任务的进程
4 backend -用于存储消息/任务结果
5 Beat -定时任务调度器，根据配置定时将任务发送给Broker
```

# 使用

## 创建woker,broker,beckend

```python
#创建tasks.py文件,声明消息队列，结果储存
from celery import Celery
app = Celery('demo',
             broker='redis://@127.0.0.1:6379/1',
             backend='redis://@127.0.0.1:6379/2',
             )
# 创建任务函数
@app.task
def task(a, b):
    print("task is running")
    return a + b
#tasks.py文件同级目录创建worker
celery -A tasks worker --loglevel=info
```

## 创建生产者

```python
from tasks import task
task.delay(a,b)
```

# Django + Celery

## 创建woker,broker,beckend

在settings.py同级目录下创建 celery.py文件

```python
from celery import Celery
from django.conf import settings
import os

# 为celery设置环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'test_celery.settings')

# 创建应用
app = Celery("test_celery")
# 配置应用
app.conf.update(
    # 配置broker
    BROKER_URL=
    'redis://:@127.0.0.1:6379/1',#redis
    'amqp://user:passwd@host:port/myvhost'#rabbitmq
)
# 设置app自动加载任务
app.autodiscover_tasks(settings.INSTALLED_APPS)
```

## 声明异步的功能

在应用模块下创建tasks.py文件

```python
from test_celery.celery import app
import time

@app.task
def task_test():
    print("task begin....")
    time.sleep(10)
    print("task over....")
```

## 使用异步的功能

在view.py内使用

```python
from django.http import HttpResponse
from .tasks import task_test
import datetime

def test_celery(request):
    task_test.delay()
	now = datetime.datetime.now()
    html = "return at %s"%(now.strftime('%H:%M:%S'))
    return HttpResponse(html)
```

## 启动Django，启动 celery worker

```
celery -A test_celery worker -l info
```