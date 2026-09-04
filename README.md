# cliproxyapi-at-docker
在飞牛的docker上部署cliproxyapi，并且用docker版的mihomo进行VPN代理，还要让外网的设置能访问到项目的API服务。

主项目地址：https://github.com/router-for-me/CLIProxyAPI
前端访问地址：http://192.168.10.202:8317/management.html
将飞牛NAS的docker源都设置为国内镜像源，拉取速度快！


操作步骤：

在飞牛NAS创建项目根目录：CLIProxyAPI，创建docker-compose.yml并构建容器；

将前端管理页面management.html放入根目录下；

用设置好的config.yaml替换根目录下的config.yaml，并重启容器，注意填写项： secret-key: ""  proxy-url: "socks5://192.168.10.200:7890"  

用前端访问地址进入后台进行相关设置。

如果用的是同一个宿主机上的docker部署的mihomo进行的网络代理，还需要进行网络完整：
理解1：因为飞牛是NAS，不是路由系统，所以系统底层没有给它自动转发流量包的权限，所以必须是显式设置代理才可以让cliproxyapi通过走代理访问外网获取gemini访问交互。
理解2：项目有了翻墙能力外还需要有被局域网或互联网通过域名访问它的API服务，这样就需要1个必要条件，就是DDNS-GO动态域名解析到它的IP地址，没有独立IPV4地址，就必须用IPV6，然而DDNS-GO是部署在宿主机上的，它只能把域名解析到宿主机（飞牛）的IPV6，另外macvlan网络中，虽然容器有了跟宿主机同网段的IP，但是它无法使用IP与宿主机通信，所以让互联网的主机访问它的API服务，就必须沿用宿主机IP+端口号的方式去访问项目提供的API服务，所以只有一种方法，就是再给项目加一个brige网络，相当于项目有了两个网络接口，一个接mihomo走代理访问外网AI大模型服务交互，一个采用宿主机IP+端口号的方式与宿主机通信，让外网设备可以通过域名解析--解析到宿主机飞牛的IPV6---IPV6+端口号访问到项目的API服务。

至此大功告成。
