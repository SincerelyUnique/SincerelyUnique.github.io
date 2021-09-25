---
title: python db end-of-file记录
date: 2021-03-30 11:40:36
tags:
- python
- oracle
categories:
---

```raw
1.异常信息
sqlalchemy.exc.OperationalError: (cx_Oracle.OperationalError) ORA-03113: end-of-file on communication channel
sqlalchemy.exc.DatabaseError: (cx_Oracle.DatabaseError) ORA-03150: end-of-file on communication channel for database link
 
 
2.涉及的module
Flask-SQLAlchemy     2.4.4
SQLAlchemy           1.3.22
 
 
3.猜测
猜测1： 怀疑连接池没起作用，即每次db操作都会创建一个新的连接，而我们项目中连接db又比较多
       事先声明一下项目之前是正常的，没有遇到过end-of-file问题，且之前连接db的方式是在config.py中将变量通过env.get('xxx')
       写入ProdConfig(Config)类（该类继承自Config(Object)）中，这里变量是通过一个.cfg的配置文件集中配置的。
       大致加载过程如下：
       由于项目集成了gunicorn服务，部署到linux上是以脚本方式启动，即run.sh包含下面部分内容：
           basedir=$(dirname $0)
	       app_home=$(cd $basedir && pwd -L) || exit 1
	       CONFIG_FILE=${app_home}/app.cfg
	       [[ -e ${CONFIG_FILE} ]] && {
    	           echo "Export config file."
    	           source $CONFIG_FILE || exit 1
	       }
       app.cfg 中 配置 sqlalchemy 连接参数： 
	       export SQLALCHEMY_DATABASE_URI=oracle://user:psw@1.1.1.1:2222/?service_name=xx.xx.com
       ProdConfig类中执行：
	       from os import environ as env
	       SQLALCHEMY_DATABASE_URI = env.get('SQLALCHEMY_DATABASE_URI')
       flask项目入口create_app方法内引入：
	       db = SQLAlchemy()   # 这里db是用于orm即sqlalchemy以models方式查询
           engine = create_engine(ProdConfig.SQLALCHEMY_DATABASE_URI)  # 这里是直接使用engine以人为写sql方式查询
	       def create_app(config_file)
	           app = Flask(__name__)
	           app.config.from_object(ProdConfig)
       dao层直接引入db或者engine执行db操作（引入db和引入engine是两种不同的方式，区别是db走的是orm）
	       from dashboard import db
	       User.query.get(username)  # User继承db.Model
	       from dashboard import engine
	       df = pd.read_sql_query('select * from USER', engine)  # pandas db查询
 
       SQLALCHEMY_DATABASE_URI是Flask-SQLAlchemy约定的一个配置参数，具体可以去这个网站查看文档
       https://flask-sqlalchemy.palletsprojects.com/en/2.x/
       该插件还提供了一些其他连接参数，比如：SQLALCHEMY_POOL_SIZE（注意很多在2.4版本都是Deprecated的，在2.4新增
       SQLALCHEMY_ENGINE_OPTIONS，很多之前的配置参数移动到这里了）
 
       上面这些都是之前的正常情况下的配置，后面因为项目里集成了xxl-job，xxl-job是以脚本方式执行任务，而python
       脚本执行是和flask web app context是隔离的，即脚本访问不到web容器内的变量，而我们又想在两边统一获取变量
       入口，方便管理，这导致很多配置变量在web端通过env.get可以获取到，但是在xxl-job端就无法获取到。最后我们采
       取了修改变量获取方式，即不在使用.cfg文件存储运行时变量，而是新建了app.ini文件，将变量迁移到ini后缀的文
       件下面。当然，改过后对于web的特有的变量还是依然走ProdConfig的，即仅对于两边都需要的变量做了统一迁移。
       下面是变更配置后的改动
       config.py文件中新增NewConfig类，直接继承Object，
       将SQLALCHEMY_DATABASE_URI移动到NewConfig，当然ProdConfig内之前的也是保留的。
           class NewConfig(object):
	       config = configparser.ConfigParser() if six.PY2 else configparser.RawConfigParser()
    	   config.read(basedir + '/app.ini')
	       SQLALCHEMY_DATABASE_URI = config.get('APP', 'SQLALCHEMY_DATABASE_URI')
       flask项目入口create_app方法内将
           engine = create_engine(ProdConfig.SQLALCHEMY_DATABASE_URI)
       改为
           engine = create_engine(NewConfig.SQLALCHEMY_DATABASE_URI)
       其他不变，但是事实证明上述改动后依然出现EOF问题
猜测2： oracle db之前有做cutover，即升级为19c版本，不过在升级前就已经出现了end-of-file，并且升级后
       其他team连接db没有出现end-of-file，所以这个和db升级有关的猜测应该可以排除，属于我们自身问题
猜测3： vm网络问题，如果是网络问题的话，我们的app应该在某个时段或者所有时段一直出现该问题，然后事实是
       大部分情况下db操作都是正常无误的，仅偶尔会出现几次end-of-file，所以网络问题先暂时排除，这个可以使用
       tcpdump抓包测试 - 未尝试
猜测4： 防火墙切断数据库空闲连接
       起初不知道有这回事，这个是从github上提了一个discussion后，cx-oracle的developer回复中提及防火墙kill idle
       连接，然后网上查了下，确有其事。
       Git discussion：https://github.com/sqlalchemy/sqlalchemy/discussions/6049#discussioncomment-492159
       转自：https://blog.csdn.net/haozhongjun/article/details/78770073，未验证
       转自：https://www.cnblogs.com/zhilu-doc/p/6094424.html，未验证
       如果你的系统不是总有人访问的话，那么连接迟早会因为空闲而被切断（别让防火墙中断数据库连接）
       通常的Web应用都是无状态的连接，一般来说对于防火墙是非常友好的。但是，大多数JavaEE应用服务器都有连接池的概念。
       为了提高性能，应用服务器会预先打开并保持一些和后台数据库服务器、LDAP服务器或其他服务器的连接。这些连接通常会
       将TCP的连接永久保持，除非发生了意外的情况。
 
       我有几个项目发生的问题都是应用服务器和数据库之间由于安全级别的问题，设有不同的防火墙。在系统运行的时候，偶尔
       会发生数据库连接无法获得的错误（在系统很闲的时候）。客户怀疑应用服务器的不稳定性。通过各种工具的分析（snoop），
       发现当应用服务器的数据库连接出现问题的时候，数据库这端没有任何问题，所有的Session连接都在，但是从应用服务器发
       过来的TCP请求没有到达数据库服务器。是中间的防火墙阻断了数据库的连接。在一个空闲的 TCP 连接上，可以很长时间没有
       任何的数据流，许多 TCP/IP 的初学者都对此感到惊奇。
 
       因此当数据库连接长时间不用（这种情况很常见，例如连接池中有10个连接，由于负载很小，一直只用到前几个）。一般来说，
       防火墙软件都会定时检查空闲的连接，并将它们阻断，来保证一些异常的中断连接被清除。
 
       这样，我们对数据库连接问题的原因找到了，由于空闲的数据库连接长时间不用被防火墙给阻断是主要的原因。被阻断了的
       数据库连接在使用的时候不会自动重新连接吗？大多数应 用服务器所使用的专业的数据库连接池都会有自动重新连接来解决
       这个问题。也有一些客户使用自己的连接池，或者一些简单的开源的方案，没有自动重联的功能， 导致应用不可用。就算有
       的连接池能够自动重连中断的连接，也不会时时刻刻都去检查这些在连接池中的连接是否被可用。
 
       因为这种检查非常消耗时间，还会影响应用，因此会每隔几分钟检查一次。在间隔时间内发生的连接中断仍然会引起系统的错误。
 
       另外的解决方案 就是从数据库服务器端进行配置，保证连接的畅通。例如在Oracle中可以设置SQLNET.EXPIRE_TIME小于防火墙
       的中断时间，就能够是 Oracle的数据库连接在没有数据交换的情况下，由服务器端自动发出探测的数据报，使得防火墙不再认
       为数据库连接是空闲的连接。
      （dead connections）
 
 
 
4.查阅
（1）sqlalchemy官方doc。
地址：https://docs.sqlalchemy.org/en/13/core/pooling.html#using-connection-pools-with-multiprocessing
The Engine returned by the create_engine() function in most cases has a QueuePool integrated, 
pre-configured with reasonable pooling defaults
可以看到官方默认集成了连接池，也就是QueuePool，并且预先设定了一些参数，比如pool size=5等等
可以通过打印日志验证：logger.info(engine.pool.status())
 
（2）因为我们是gunicorn server，会启动16个进程（后改为9个进程，即2n+1，n为cpu数量），每个进程会持有一个pool，
并且sqlalchemy创建db连接池的方式并不是预先创建好，可以理解为懒创建而不是预创建，比如当项目刚启动时其实是
没有连接池的，只有当后端接收到一个请求需要连接db时才会去对应的相应请求的worker下面创建一个隶属于该worker
的TCP连接，而且并不会根据你设置的最小连接数直接创建多个连接，而是根据需要先创建1个，后续根据实际的db并发连接数
陆续增加TCP连接，并在这个连接中建立session执行db操作，stackoverflow对于gunicorn db connection pool的连接描述
地址：https://stackoverflow.com/questions/60233495/choosing-db-pool-size-for-a-flask-sqlalchemy-app-running-on-gunicorn
the maximum number of database connections is (number of workers) * N where N = pool_size + max_overflow
 
（3）flask-sqlalchemy db connection pool配置
地址：https://flask-sqlalchemy.palletsprojects.com/en/2.x/config/
可以看到SQLALCHEMY_POOL_SIZE、SQLALCHEMY_POOL_TIMEOUT等很多参数在v2.4已经deprecated了，但是2.4新增了
一个参数SQLALCHEMY_ENGINE_OPTIONS，可以在这个参数里指定一些pool属性
    SQLALCHEMY_ENGINE_OPTIONS = {
        'pool': QueuePool,
        'pool_recycle': 280,  # which simply recycles connections after the given number of seconds
        'pool_size': 20,
        'pool_timeout': 100,
        'pool_pre_ping': True,
        'connect_timeout': 3,
        'echo': False,
        'echo_pool': False,
        "max_overflow": 5
    }
当flask web启动后会直接初始化该配置参数并生效。
 
（4）获取oracle当前某个machine的活跃连接及查询脚本
select t1.machine, t1.prev_exec_start, t1.logon_time, t1.sid, t1.serial#,
       t1.username, t1.status, t1.osuser, t1.port,
       t1.service_name, t1.terminal, t1.sql_id, t2.sql_text
  from v$session t1, v$sql t2 where t1.username='xxx' and machine='xxx' and t1.status!='INACTIVE';
 
（5）尝试使用cx-oracle自身实现的pool，禁用sqlalchemy实现的pool
cx-oracle是python DBAPI（可以理解为java jdbc），而sqlalchemy是python orm框架（可以理解为Hibernate），
所以sqlalchemy底层是基于cx-oracle的。
如果不想用sqlalchemy的pool，需要在实例化sqlalchemy时绑定poolClass为NullPool，因为sqlalchemy默认是
使用QueuePool的。使用cx-oracle自身的pool的方式如下：
    dsn = "(DESCRIPTION = " \
          " (ADDRESS = (PROTOCOL = TCP)(HOST = x.x.x.x)(PORT = xxxx))" \
          " (ADDRESS = (PROTOCOL = TCP)(HOST = x.x.x.x)(PORT = xxxx))" \
          " (ADDRESS = (PROTOCOL = TCP)(HOST = x.x.x.x)(PORT = xxxx))" \
          " (LOAD_BALANCE = YES) " \
          " (CONNECT_DATA = (SERVER = DEDICATED)" \
          "                 (SERVICE_NAME = xxx.xxx.com) " \
          "                 (FAILOVER_MODE = (TYPE = SELECT) " \
          "                 (METHOD = BASIC) " \
          "                 (RETRIES = 3)" \
          "                 (DELAY = 5))))"
    pool = cx_Oracle.SessionPool(
        user="xxx",
        password="xxx",
        dsn=dsn,
        min=10,
        max=20,
        increment=1,
        threaded=True
    )
    engine = create_engine("oracle://", creator=pool.acquire, poolclass=NullPool, echo=True, echo_pool=True)
    sql = "select * from table"
    pd.read_sql_query(sql, engine)
 
（6）TCP连接检测
因为猜测可能是db server端因为防火墙等原因断开和我们app client端的连接，所以这里有做监测。并且在监测时多次发现
每当周末过后，比如周一刚上班时，会发现部分TCP连接处于CLOSE_WAIT状态，并且过一会如果有用户从web访问db时，就会
出现oracle EOF问题。
简单查看服务器python进程对应TCP连接状态：
    ps -efl|grep python    （看python pid）
    netstat -tnap|grep 1202   （看每个pid对应的TCP连接状态，1202为db server端口）
    netstat -tnap|grep 1202|wc -l  （TCP连接数）
参考：https://www.cnblogs.com/Serverlessops/articles/9367534.html
CLOSE_WAIT：在TCP四次挥手期间，被动关闭端收到FIN包后，进入CLOSE_WAIT状态
 
四次挥手大致过程
Client: 服务端大哥，我事情都干完了，准备撤了，这里对应的就是客户端发了一个FIN
Server：知道了，但是你等等我，我还要收收尾，这里对应的就是服务端收到 FIN 后回应的 ACK
 
经过上面两步之后，服务端就会处于 CLOSE_WAIT 状态。过了一段时间 Server 收尾完了
 
Server：小弟，哥哥我做完了，撤吧，服务端发送了FIN
Client：大哥，再见啊，这里是客户端对服务端的一个 ACK
到此服务端就可以跑路了，但是客户端还不行。为什么呢？客户端还必须等待 2MSL 个时间，这里为什么客户端还不能直接
跑路呢？主要是为了防止发送出去的 ACK 服务端没有收到，服务端重发 FIN 再次来询问，如果客户端发完就跑路了，那么
服务端重发的时候就没人理他了。并且这个等待的时间长度也很讲究。
Maximum Segment Lifetime 报文最大生存时间，它是任何报文在网络上存在的最长时间，超过这个时间报文将被丢弃。
 
（7）sqlalchemy Connection类描述
     The Connection object represents a single DBAPI connection checked out 
     from the connection pool. In this state, the connection pool has no affect 
     upon the connection, including its expiration or timeout state. For the 
     connection pool to properly manage connections, connections should be 
     returned to the connection pool (i.e. ``connection.close()``) whenever the
     connection is not in use.
 
（8）flask 日志格式定义
     $(name)s: Logger的名字(root):l = logging.getLogger();l.name
     %(levelno)d: 打印日志级别的数字
     %(levelname)s: 打印日志级别名称
     %(pathname)s: 当前执行程序的路径,相当于sys.argv[0]
     %(filename)s: 当前执行程序文件名
     %(funcName)s: 打印日志的当前函数名
     %(lineno)d: 打印日志的当前行号
     %(asctime)s: 打印日志的时间
     %(thread)d: 打印线程ID
     %(threadName)s: 打印线程名字
     %(process)d: 打印进程ID
     %(message)s: 日志信息【重点要记录的信息】
 
（9）sqlalchemy-collectd
sqlalchemy连接监控模块，也是sqlalchemy团队自己研发的，但是因为缺少文档，我没有build成功
 
（10）tnsnames.ora 说明文档（曾怀疑是TNS连通性问题）
https://docs.oracle.com/database/121/NETRF/tnsnames.htm#NETRF282
http://www.dba-oracle.com/t_troubleshooting_sql_net_connectivity_errors.htm
 
 
 
5.解决
最开始因为没有太多时间细察这个问题，我们做了一些保护，就是一旦遇到EOF问题后，服务器自动通
过API重启app（最开始是自己重启自己，后面是通过外部服务启动问题app）。
 
后面我们认为是pool没有生效，所以尝试显式配置QueuePool，指定最小连接10，最大连接20，最大允许超出5个连接，
并且指定每次自增连接数量为1，部署后未生效，还是会出现EOF问题。
 
中间也有找sqlalchemy开发讨论，他们其实在测试时也遇到了很多EOF问题，对于具体的rootcause也不
是很了解，因为EOF毕竟涉及的比较多，然后找cx-oracle的开发一起帮忙看了一下，后续我们将目光转
移到连接池的TCP连接上。
 
目前我们尝试在创建engine时，通过dsn配置，使其HA（我们db是多台且LB，我们之前只使用了其一）
SQLALCHEMY_DATABASE_URI = oracle://user:psw@(DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = xx.xx.x.xxx)(PORT = xxx))(ADDRESS = (PROTOCOL = TCP)(HOST = xx.xx.xx.xx)(PORT = xx)) (ADDRESS = (PROTOCOL = TCP)(HOST = xx.xx.xx.xx)(PORT = xx)) (LOAD_BALANCE = YES) (CONNECT_DATA = (SERVER = DEDICATED)(SERVICE_NAME = xxx.xxx.xxx) (FAILOVER_MODE = (TYPE = SELECT) (METHOD = BASIC) (RETRIES = 3)(DELAY = 5))))
在使用orm执行db操作时，将poolClass置为NullPool，不再使用orm的连接池
SQLALCHEMY_ENGINE_OPTIONS = {'poolclass': NullPool}
目前还处于观测中，后续如有问题及时更新
 
 
 
7.参考：
https://blogs.oracle.com/opal/how-to-use-python-flask-with-oracle-database
https://pypi.org/project/DBUtils/  （pip install DBUtils，未尝试）
https://www.cnblogs.com/Serverlessops/articles/9367534.html
 
 
 
 
 
 
 
 
 
 
```
