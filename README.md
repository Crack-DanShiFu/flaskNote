# python-Flask学习笔记



## 环境搭建

## HelloWorld

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

外部可访问的服务器



如果你运行了这个服务器，你会发现它只能从你自己的计算机上访问，网络中其它任何的地方都不能访问。在调试模式下，用户可以在你的计算机上执行任意 Python 代码。因此，这个行为是默认的。

如果你禁用了 debug 或信任你所在网络的用户，你可以简单修改调用 [`run()`](http://docs.jinkan.org/docs/flask/api.html#flask.Flask.run) 的方法使你的服务器公开可用，如下:

```
app.run(host='0.0.0.0')
```

这会让操作系统监听所有公网 IP。



## 调试模式

```python
app.debug = True
app.run()
```

```python
app.run(debug=True)
```

**在pycharm中需要在启动器中勾选Debug模式**

## 路由（route）

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello World'
```

route() 装饰器把一个函数绑定到对应的 URL 上。

## Url传参

```python
@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return 'User %s' % username

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return 'Post %d' % post_id
```

可以用 `<converter:variable_name>` 指定一个可选的转换器。

转换器有下面几种：

| int   | 接受整数                   |
| ----- | -------------------------- |
| float | 同 int ，但是接受浮点数    |
| path  | 和默认的相似，但也接受斜线 |

## 唯一Url



```python
@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'
```

**其中：**

**hostname：port/projects/    和**

**hostname：port/projects**

**都能访问而**

**hostname：port/about/    报错Not  found**



## 构造url

- 不带参数：

```python
url_for('index')
```

其中index为视图函数的方法名

- 带参数：

```python
url_for('profile', username='John Doe')
```

其中username为参数

## 指定http请求方式

默认情况下，路由只回应 GET 请求

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        do_the_login()
    else:
        show_the_login_form()
```

**相关：**

HTTP 方法（也经常被叫做“谓词”）告知服务器，客户端想对请求的页面 *做* 些什么。下面的都是非常常见的方法：

- GET

  浏览器告知服务器：只 *获取* 页面上的信息并发给我。这是最常用的方法。

- HEAD

  浏览器告诉服务器：欲获取信息，但是只关心 *消息头* 。应用应像处理 GET 请求一样来处理它，但是不分发实际内容。在 Flask 中你完全无需 人工 干预，底层的 Werkzeug 库已经替你打点好了。

- POST

  浏览器告诉服务器：想在 URL 上 *发布* 新信息。并且，服务器必须确保 数据已存储且仅存储一次。这是 HTML 表单通常发送数据到服务器的方法。

- PUT

  类似 POST 但是服务器可能触发了存储过程多次，多次覆盖掉旧值。你可 能会问这有什么用，当然这是有原因的。考虑到传输中连接可能会丢失，在 这种 情况下浏览器和服务器之间的系统可能安全地第二次接收请求，而 不破坏其它东西。因为 POST它只触发一次，所以用 POST 是不可能的。

- DELETE

  删除给定位置的信息。

- OPTIONS

  给客户端提供一个敏捷的途径来弄清这个 URL 支持哪些 HTTP 方法。 从 Flask 0.6 开始，实现了自动处理。

在 HTML4 和 XHTML1 中，表单只能以 GET 和 POST 方法提交到服务器。但是 JavaScript 和未来的 HTML 标准允许你使用其它所有的方法。此外，HTTP 最近变得相当流行，浏览器不再是唯一的 HTTP 客户端。比如，许多版本控制系统就在使用 HTTP。



## *加载静态文件

#### 加载图片：

```python
url_for('static', filename='images/logo.png')
<img src="{{ url_for('static',filename='images/aaa.jpg') }}" alt="">
```

对应存储在文件系统上的 `static/images/logo.png` 。

#### 加载CSS

```python
<link rel="stylesheet" href="../static/css/index.css">
#或者
<link rel="stylesheet" href="{{ url_for('static',filename='css/index.css') }}">
```

相关：

 在开发测试时，可以对 flask 做以下配置：

`TEMPLATES_AUTO_RELOAD = True`
`SEND_FILE_MAX_AGE_DEFAULT = 0`

这样每当你的 模板文件或静态文件改变时，就能即时在浏览器里看到更改后的效果  

或者在浏览器设置 Disable cache

#### 加载js

<script src="{{ url_for('static',filename='js/index.js') }}"></script>



## 模板渲染

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

在html页面可通过{{name}}取得参数



```html
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}
```
