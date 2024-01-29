# Offine-Text-Translate 本地离线文字翻译

支持多语言的本地离线文字翻译，提供api接口。

>
>
> 本项目是基于 [开源项目LibreTranslate](https://github.com/LibreTranslate/LibreTranslate) 的再封装，目的是提供一个容易在本地机器上直接部署的翻译Api服务，无需docker，并提供 Windows 预编译exe包，不必部署，双击可用，方便新手和小白使用。
>
> 第一次启动需要下载模型，后续即可离线运行
>
> 如果你想使用原生LibreTranslate项目或想部署在docker，请访问 https://github.com/LibreTranslate/LibreTranslate
>

# Windows 预编译包使用

0. 如果你无法打开 https://raw.githubusercontent.com 这个地址，必须在 set.ini 中 `PROXY=` 设置代理地址，比如 v2ray默认地址是 `http://127.0.0.1:10809`，则填写 `PROXY=http://127.0.0.1:10809`  clash默认`http://127.0.0.1:7890`，则填写 `PROXY=http://127.0.0.1:7890`，仅在下载模型时使用，模型下载后即可去掉代理

[你也可以从百度网盘下载已打包好的模型，解压后将里面的".local" 文件夹复制覆盖到本软件根目录下](https://pan.baidu.com/s/1h5upbQIQw3LmUU6-3-YRbw?pwd=72bj)

1. [点击下载最新版window预编译包](https://github.com/jianchang512/ott)，解压到无空格的英文目录下，双击 start.exe

2. 第一次启动后会自动下载模型，下载完毕后会显示当前Api服务的地址和端口，就可以使用了
(你也可以从百度网盘下载已打包好的模型，解压后将里面的".local" 文件夹复制覆盖到本软件根目录下)


3. 可以自己编写程序请求该Api服务，实现替代百度翻译等功能，或者填写到一些需要翻译功能的软件中，比如若要用在[视频软件配音](https://github.com/jianchang512/pyvideotrans)中，在软件菜单-设置-OTT中填写 服务器地址和端口即可(默认http://127.0.0.1:9911)

# Window上源码部署

0. 首先到 python.org 下载 python3.9+版本并安装，建议3.10，在安装时仔细查看，选择 “Add ... Path”复选框，以方便后续使用

1. window上安装git客户端，[点击去下载](https://git-scm.com/download/win)，选择下载 64-bit Git for Windows Setup，下载后双击安装，一路下一步直到完成

2. 创建一个空目录，比如在 D盘下创建目录 ott，然后进入该目录 `D:/ott`,在文件夹地址栏输入 `cmd` 后回车，在打开的cmd黑窗口中输入`git clone https://github.com/jianchang512/ott .` 回车执行.

3. 创建虚拟环境，在刚刚的cmd窗口中继续输入命令 `python -m venv venv` 回车

>     此处注意：如果提示"python 不是内部或外部命令，也不是可运行的程序",说明 第0步 安装时未选中复选框，重新双击已下载的 Python安装包，选择“Modify”,然后注意选中“Add ... Path”。
>
>     重新安装python完毕后，**必须关闭已打开的cmd窗口**,否则可能还是提示命令未找到，然后进入`D:/ott`,地址栏输入`cmd`回车，再重新执行`python -m venv venv`
>

4. 上步命令执行成后，继续输入 `.\venv\scripts\activate` 回车，再执行 `pip install -r requirements.txt --no-deps`, 如果提示“not found version xxx”，请将将镜像源改为pip官方或者阿里云镜像

5. 如果需要启用cuda加速翻译，则继续分别执行
    `pip uninstall -y torch`

    `pip install torch==2.1.2 --index-url https://download.pytorch.org/whl/cu121`

6. 在set.ini中设置代理 PROXY=代理地址，比如如果你的代理地址是 http://127.0.0.1:10189,那么填写后`PROXY=http://127.0.0.1:10189`

7. 执行启动服务命令，`python start.py`

# Mac 或 Linux 部署

0. 先确认是否安装了正确的python版本3.9+和git， `python -V` or `python3 -V`

1. 创建空目录并进入，比如 `/data/ott && cd /data/ott`, 然后拉取源码 `git clone https://github.com/jianchang512/ott .`

2. 创建虚拟环境并激活  `python3 -m venv venv && . ./venv/bin/activate`

3. 安装依赖，`pip3 install -r requirements.txt --no-deps`，如果提示“not found”，同样将镜像改为 pip官方或阿里云镜像

4. 如果要启用cuda加速翻译，则继续执行

    `pip3 uninstall -y torch`

    `pip3 install torch==2.1.2 --index-url https://download.pytorch.org/whl/cu121`

5. 在 she.ini中设置代理地址

6. 启动服务 `python3 start.py`

注意如果你的python执行命令不是`python3`，上面命令中的 python3需替换为对应的命令比如 `python`

# Api接口使用示例

假如你部署的地址和端口是 `http://127.0.0.1:9911`,请求api代码如下

API地址：http://127.0.0.1:9911

**python requests 请求示例**
```

import requests

result=requests.post("http://127.0.0.1:9911/translate",json={"q":"你好啊我的朋友","source":"zh","target":"en"})
print(result.json())

# 输出如下
{'translatedText': 'Hello, my friend'}
```



**js fetch请求**
```
const res = await fetch("http://127.0.0.1:9911/translate", {
  method: "POST",
  body: JSON.stringify({
    q: "Hello!", // 请求翻译的文本
    source: "en",//原始语言，或者填写 'auto' 自动检测
    target: "es"//目标语言
  }),
  headers: { "Content-Type": "application/json" }
});
console.log(await res.json());
// 返回json相应
{
    "translatedText": "¡Hola!" // translatedText 字段中是翻译后的文本
}
```


# 对 argostranslate 的修改以实现下载模式出错时提醒代理

1. 修改了  `\venv\Lib\site-packages\argostranslate\networking.py` 的 get 方法，当下载失败时，抛出异常


```
def get(url: str, retry_count: int = 3) -> bytes | None:
    """Downloads data from a url and returns it

    Args:
        url: The url to download (http, https)
        retry_count: The number of retries to attempt if the initial download fails.
                If retry_count is 0 the download will only be attempted once.

    Returns:
        The downloaded data, None is returned if the download fails
    """
    if get_protocol(url) not in supported_protocols:
        return None
    info(f"Get {url}")
    print(f'{url=}')
    download_attempts_count = 0

    while download_attempts_count <= retry_count:
        try:
            req = urllib.request.Request(
                url,
                headers={"User-Agent": USER_AGENT},
            )
            response = urllib.request.urlopen(req)
            data = response.read()
            info(f"Got {url}")
            return data
        except Exception as err:
            download_attempts_count += 1
            # 增加抛出异常
            raise Exception('download error')
            error(err)
    # 增加抛出异常
    raise Exception('download error')
    return None
```

2. `\venv\Lib\site-packages\argostranslate\package.py`, 注释掉无法连接raw.githubusercontent.com时的报错，已在入口文件做了处理



```
def update_package_index():
    """Downloads remote package index"""
    with package_lock:
        try:
            response = urllib.request.urlopen(settings.remote_package_index)
        except Exception as err:
            # 注释掉错误输出，已在入口文件获取了index.json，此处如果无法连接则不再输出错误，避免造成小白迷惑
            #error(err)
            return
        data = response.read()
        with open(settings.local_package_index, "wb") as f:
            f.write(data)
```


# CUDA 加速支持

**安装CUDA工具** [详细安装方法](https://juejin.cn/post/7318704408727519270)


安装好CUDA后，如果有问题，执行 `pip uninstall -y torch`，然后执行`pip install torch==2.1.2 --index-url https://download.pytorch.org/whl/cu121`。

安装完成后执行 `python testcuda.py` 如果输出均是  True ,说明可用

有时会遇到“cublasxx.dll不存在”的错误, 或者未遇到此错误，并且CUDA配置正确，但始终出现识别错误，需要下载 cuBLAS，然后将dll文件复制到系统目录下

[点击下载 cuBLAS](https://github.com/jianchang512/stt/releases/download/0.0/cuBLAS_win.7z)，解压后将里面的dll文件复制到 C:/Windows/System32下


# 基于以下开源项目

1. https://github.com/LibreTranslate/LibreTranslate
2. https://github.com/argosopentech/argos-translate