# Ansible 是什么？

ansible是一种自动化运维工具,基于paramiko开发的,并且基于模块化工作，Ansible是一种集成IT系统的配置管理、应用部署、执行特定任务的开源平台，它是基于python语言，由Paramiko和PyYAML两个关键模块构建。集合了众多运维工具的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能.ansible是基于模块工作的,本身没有批量部署的能力.真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架.ansible不需要在远程主机上安装client/agents，因为它们是基于ssh来和远程主机通讯的.

ansible被定义为配置管理工具,配置管理工具通常具有以下功能:

* 确保所依赖的软件包已经被安装
* 配置文件包含正确的内容和正确的权限
* 相关服务被正确运行
# Ansible 和其她运维工具的比较
| 项目              | Puppet                                                | SaltStack                                | Ansible                                         |
|-----------------|-------------------------------------------------------|------------------------------------------|-------------------------------------------------|
| 开发语言            | Ruby                                                  | Python                                   | Python                                          |
| 是否有客户端          | 有                                                     | 有                                        | 无                                               |
| 是否支持二次开发        | 不支持                                                   | 支持                                       | 支持                                              |
| 服务器与远程机器是否相互验证  | 是                                                     | 是                                        | 是                                               |
| 服务器与远程机器的通信是否加密 | 是，标准的SSL协议                                            | 是，使用AES加密                                | 是，使用OpenSSH                                     |
| 平台支持            | AIX , BSD, HP\-UX, Linux , Mac OSX , Solaris, Windows | BSD, Linux , Mac OS X , Solaris, Windows | AIX , BSD , HP\-UX , Linux , Mac OS X , Solaris |
| 是否提供Web UI      | 提供                                                    | 提供                                       | 提供，但是是商业版本                                      |
| 配置文件格式          | Ruby 语法格式                                             | YAML                                     | YAML                                            |
| 命令行执行           | 不支持，大师可以通过配置模块实现                                      | 支持                                       | 支持                                              |
# Ansible 是什么样子的
## 架构图
[架构图](https://github.com/wmenjoy/awesome-knowleges/blob/master/devos/Ansible1.png?raw=true)

上图为ansible的基本架构，从上图可以了解到其由以下部分组成：

* **核心**：ansible
* **核心模块（Core Modules）**：这些都是ansible自带的模块
* **扩展模块（Custom Modules）**：如果核心模块不足以完成某种功能，可以添加扩展模块
* **插件（Plugins）**：完成模块功能的补充
* **剧本（Playbooks）**：ansible的任务配置文件，将多个任务定义在剧本中，由ansible自动执行
* **连接插件（Connectior Plugins）**：ansible基于连接插件连接到各个主机上，虽然ansible是使用ssh连接到各个主机的，但是它还支持其他的连接方法，所以需要有连接插件
* **主机群（Host Inventory）**：定义ansible管理的主机
## 原理图



