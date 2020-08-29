# open-license-manager

# 1.下载源码
	https://github.com/open-license-manager
	open-license-manager 
# 2.下载包含源码
	lcc-license-generator 
	下载后拷贝入 open-license-manager 的 extern 目录

# 3.编译
	1.下载boost
		1.地址
			https://sourceforge.net/projects/boost/files/boost-binaries/
		2.版本
			预编译版本 有很多
			14.1 对应 2017
			14.2 对应 2019
		3.	应选择 msvc14.1
			选择 msvc14.2 不行,无法编译成功
	2.编译
		1.用qtcreator 直接 打开 CMakeLists.txt
		2.添加以下两句,否则无法找到 boost
			SET( BOOST_ROOT D:/boost_1_73_0_14_1_64 )
			SET( Boost_INCLUDE_DIR D:/boost_1_73_0_14_1_64 )
		3.选择重新构建
			1.生成,3个lib
				1.licensecc_static.lib
					用于控制 程序 认证
				2.license_generator_lib.lib
					用于生成认证 许可
				3.license_generator_snippet.lib
					用于测试
					估计无用
					
# 4.软件 授权 使用
	1.关于 boost
		open-license-manager 编译的时候 对 boost 使用的时 静态链接
		所以使用 open-license-manager 的时候无需 添加 INCLUDEPATH += D:/boost_1_73_0_14_1_64

	2.包含 olm
		1.包含文件 有三个
			1.licensecc/datatypes.h \
			2.licensecc/licensecc.h \
			3.licensecc_properties.h
		2.包含库有一个
			win32: LIBS += -L$$PWD/./ -llicensecc_static
		3.依赖库有一个
			win32: LIBS += -LD:/mid/Anaconda3/pkgs/openssl-1.1.1c-he774522_1/Library/lib/ -llibcrypto
			这个依赖库很吊诡,不知 3.2 中编译的时候项目是如何 找到 , openssl 的
	3.软件包含以上 h 文件 和 lib 文件 后就可以判断是否有 经过授权了

# 5.授权文件 生成
	1.经过 3 的编译,会在 extern/license-generator/src/license_generator 目录下生成 lccgen.exe
		lccgen.exe 的编译 是 对 license_generator_lib.lib 做了静态链接的
		所以 lccgen.exe 可以独立运行
	2.发放授权
		源代码 目录 下 有 projects/DEFAULT 目录
		将 lccgen.exe 拷贝到该目录下,即可用默认参数 运行 lccgen.exe 生成 lic 文件
		生成后 拷贝 入 被控制软件的 根目录 既可以 完成授权
	3. ./lccgen.exe  license issue -o licenses/user.lic -s AADY-YRDq-MAA= -e 20200526
		以上命令 即可以 在 licenses 目录下 生成 user.lic 文件
		-o 指定 lic 文件 目录
		-s 指定 被控制软件 运行 机器 的 id
		-e 指定 有效期结束时间
	4.帮助
		1.命令行帮助
			./lccgen.exe  license issue --help
		2.网页帮助
			http://open-license-manager.github.io/open-license-manager/usage/issue-licenses.html#issue-licenses			

# 6.授权
	1.授权个性化
		1.magic num
			LCC_PROJECT_MAGIC_NUM
			可通过 set(LCC_PROJECT_MAGIC_NUM 0) 在编译阶段设置此值
			默认为 0,在 open-license-manager/src/CMakeLists.txt 中检测并设置默认值
		2.feature_name
			在发放授权时,通过 -f 参数指定
			
	2.个性化授权 使用
		1.使用者 acquire_license
			其第一个参数可以用来指定 magic 和 feature_name
			可用来 防止 第三方 授权
	3.案例
		1.默认 magic + 默认使用 程序名称 授权
			1.编译时 不指定 magic num
			2.授权配置
				1.使用 DEFAULT 做 授权项目 目录名称
				2.软件中不指定 CallerInformations 数据,传入 nullptr 参数
			3.授权命令
				./lccgen.exe  license issue -o ~/Desktop/user_ui.lic -s AADY-YRDq-MAA= -e 20200528

		2.默认 magic + 个性化授权名称
			1.编译时 不指定 magic num
			2.授权配置
				1.使用 user 做授权项目 目录名称
				2.软件中指定 CallerInformations.feature_name 为 user
			3.授权命令
				./lccgen.exe  license issue -o ~/Desktop/user_ui.lic -s AADY-YRDq-MAA= -e 20200528

		3.多 功能控制(未成功)
			如果一个程序 有若干 个功能需要授权控制,可以通过 -f 参数 在授权时 指定 多个 授权名称
				./lccgen.exe  license issue -o ~/Desktop/user_ui.lic -s AADY-YRDq-MAA= -e 20200528 -f laoxie
				在程序中可以通过检测 这些名称 来控制 模块的 使用状态
			经过测试,此功能尚不能使用,只有指定为 和 目录名称 相同时 才可以 成功调用,如此便没有指定的意义
			
	4.实际
		1.创建 tagTrader 授权项目名称,大小写在win下不敏感
		2.特定机器绑定 限时 授权命令
			./lccgen.exe  license issue -o ~/Desktop/user_ui.lic -s AADY-YRDq-MAA= -e 20200528
		3.不特定机器绑定 限时 授权命令
			./lccgen.exe  license issue -o ~/Desktop/user_ui.lic -e 20200528
		4.目标 *.lic 文件名称 可以任意取,但是若需要程序在默认目录下按默认名称读取的时候,则需要命名为与 程序运行名称 一致的名称
		5. tagTrader 这个名称 在 编译交易 软件的时候 是 硬编码 在代码里面的,虽然可以 参数化,但是不必参数化,也增加了破解难度


		
