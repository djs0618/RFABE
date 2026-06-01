# RABE: Revocable Fast Attribute-Based Encryption

  基于可撤销属性基加密的高效云医疗系统。

## 项目概述

  本项目面向云存储环境下的医疗数据访问控制需求，构建了一个以电子病历安全存储与细粒度访问控制为核心的高效云医疗系统。系统采用"用户层—应用层—加密层—存储层"的四层架构，充分满足病历文件高效加解密、多维度权限管理及细粒度访问控制的要求。项目包含两大核心模块：RFABE 可撤销属性基加密方案，基于分段式密钥生成机制，实现支持即时用户撤销的高效 CP-ABE 方案，包含 Setup、KeyGen、Encrypt、Decrypt、KeyUpdate 完整算法流程；高效云医疗管理系统，基于 RFABE 方案构建的完整医疗数据隐私保护平台，包含管理员、医生、患者三大角色模块，实现病历数据的安全存储与细粒度访问控制。

## 功能特性

  RFABE 核心算法包括 Setup 生成系统主密钥与公共参数，KeyGen 基于用户属性集合生成分段式私钥，Encrypt 基于访问策略加密明文数据，Decrypt 满足策略的用户解密密文，KeyUpdate 生成密钥更新信息实现即时撤销。云医疗管理系统包括管理员模块负责科室管理、医生管理、患者管理、系统配置、公告发布、医生排班、用户撤销，医生模块负责查看排班、患者叫号、填写病历、查看就诊记录、住院登记、个人信息管理，患者模块负责预约挂号、查看就诊记录、设置病历访问策略、住院信息查询、电子钱包管理、个人信息管理。策略管理与访问控制支持基于属性的细粒度访问策略定义，支持用户属性动态分配与权限管理，支持用户撤销后的密钥更新与访问权限回收，提供策略可视化配置界面。

## 技术特色

  轻量级算存优化采用 AES 对称加密与 CP-ABE 非对称加密结合的混合加密机制，大幅降低加解密计算开销，适配医疗终端资源受限场景。动态属性空间扩展突破传统 ABE 方案初始化阶段固定属性全集的局限，支持无界属性空间动态管理，灵活适配医疗权限复杂性。即时用户撤销机制引入基于完全二叉树的用户撤销技术，通过最小覆盖集算法与密钥更新机制，实现离职医生权限的即时回收。患者自主指定策略赋予患者病历访问策略制定权，支持根据诊疗场景自定义细粒度访问策略，掌握数据隐私主导权。

## 系统架构

  用户层包括患者、管理员和医生三种角色，各角色通过浏览器访问 Vue.js 设计的前端页面，系统根据用户角色自动显示对应的页面，所有操作均通过 AJAX/REST 获取，加入令牌进行身份认证和安全加密通信。应用层承接用户层的操作请求，实现各类医疗业务功能与访问规则的初步处理。加密层采用混合加密机制保障病历安全，先以 AES 算法对病历明文进行加密，再基于患者设定的 CP-ABE 访问策略对 AES 密钥进行非对称加密。存储层采用"云存储 + 本地存储"的混合架构，云端存储加密后的病历密文与访问策略，本地存储属性库与审计日志库。

## 依赖环境

  基础环境包括 Python 3.8+、Java 1.8+、Node.js 16+、MySQL 8.0+。Python 密码学库包括 Charm-Crypto 0.5（需先安装 GMP、PBC 库）、pyparsing。开发工具包括 IntelliJ IDEA / PyCharm、Maven 3.8、Navicat 16。

## 文件说明

  Python 核心算法层包括 FABEO.py 基础分段式 CP-ABE 包括 Setup/KeyGen/Encrypt/Decrypt，time.py 时间管理树实现，keygen_abe.py 密钥生成算法，decrypt_abe.py 解密算法，encrypt_abe.py 加密算法，keyupdate_abe.py 密钥更新算法，Rabe.py 可撤销 ABE 主程序包括用户撤销树、时间策略、密钥更新，Msp.py LSSS 访问结构实现，policytree.py 策略解析工具，secretutil.py 密钥工具类。Java 业务层包括 springboot/ 目录下 Spring Boot 后端项目包含 Controller、Service、Mapper 层，vue/ 目录下 Vue.js 前端项目包含管理员、医生、患者模块。

## 安装部署

  环境准备阶段，安装 gcc、make、perl 执行 sudo apt install gcc make perl，安装 m4、flex、bison、libssl-dev 执行 sudo apt update 和 sudo apt install m4 flex bison subversion libgmp-dev，安装 Python 及依赖执行 sudo ln -s /usr/bin/python3 /usr/bin/python 和 pip3 install pyparsing==2.4.6，OpenSSL Ubuntu 默认已安装可验证版本。安装 GMP 从 https://gmplib.org/download/gmp/ 下载 gmp-5.1.3.tar.bz2，执行 tar -xjf gmp-5.1.3.tar.bz2、cd gmp-5.1.3、sudo ./configure、sudo make、sudo make install。安装 PBC 从 https://crypto.stanford.edu/pbc/ 下载 pbc-1.0.0.tar.gz，执行 tar -xzf pbc-1.0.0.tar.gz、cd pbc-1.0.0、sudo ./configure、sudo make、sudo make install。安装 Charm-Crypto 从 https://github.com/JHUISI/charm 下载，执行 cd charm-dev、sudo ./configure.sh、sudo make、sudo make install，若出现 fatal error: openssl/ssl.h: No such file or directory 则执行 sudo apt-get install libssl-dev。启动后端服务需修改 application.yml 中的数据库用户名和密码，通过 MySQL 可视化工具 Navicat 导入 xm_hospital_manager.sql，运行 SpringbootApplication.java。启动前端服务执行 cd vue、npm config set registry https://repo.huaweicloud.com/repository/npm/、npm install、npm run serve。

## 系统初始化账号

  管理员账号 admin1 密码 123456，医生账号 doctor1 至 doctor20 密码 123456，患者账号 user1 至 user6 密码 123456。

## 下载完整项目

  Release v1.0 - RABE3.zip (271MB) 地址为 https://github.com/djs0618/RABE/releases/tag/v1.0
