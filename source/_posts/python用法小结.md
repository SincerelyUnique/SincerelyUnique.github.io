---
title: python用法小结
date: 2023-01-04 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 操作ES

## 连接ES服务器

```python
es_host_list = ['192.168.0.1', '192.168.0.2', '192.168.0.3', '192.168.0.4']
es_username = ''
es_password = ''
es_client = Elasticsearch(hosts=, http_auth=(es_username, es_password))
```

## 插入ES数据

```python
documents = [{
    "_index": index_name,
    "_source": {
        'metrics': doc,
        '@timestamp': datetime.utcnow()
    }
} for doc in doc_list]
helpers.bulk(es_client, documents)
```

```python
data = df.to_dict(orient='records')[0]
es_client.create(index='index_name', id='doc_id', body=data, request_timeout=120)
```

## 删除ES数据

```python
es_client.delete(index='index_name', id='doc_id')
```

```python
es_client.indices.delete(index=index_name, ignore=[400, 404])
```

## 查询ES数据

```python
# for aggregation
resp = client.search(index='xxx', body=query, request_timeout=300)

# for original
resp = helpers.scan(client, query=query, index='xxx', size=10000, request_timeout=300)
for num, doc in enumerate(resp):
    data_list.append(doc.get('_source'))
```

## 更新ES数据

```python
es_client.update(index=es_index, id='es_id', body={"doc": body})
```

# python列表切片

```python
pieces = [id_list[i:i + 1000] for i in range(0, len(id_list), 1000)]
```

# 启动线程

## 单线程

```python
params = {
    'p1': '1',
    'p2': '2',
    'p3': '3'
}
action = {
    "a": method_a,
    "b": method_b,
    "c": method_c,
    "d": method_d,
    "e": method_e,
    "f": method_f,
}
thread = threading.Thread(target=action[name], kwargs=params)
thread.daemon = True
thread.start()
```

## 线程池

```python
thread_count = 20
op_size = math.ceil(len(user_list) / thread_count)
users_pieces = [user_list[i:i + op_size] for i in range(0, len(user_list), op_size)]
max_workers = len(users_pieces)
executor = concurrent.futures.ThreadPoolExecutor(max_workers=max_workers)
kwargs = {
    'p1': 'p1',
    'p2': 'p2',
    'p3': 'p3',
    'p4': 'p4'
}
futures = []
for users in users_pieces:
    futures.append(executor.submit(loop_users_, **kwargs))
for i in range(len(futures)):
    data_list = futures[i].result()
```

# 数据库查询

```python
uri = 'oracle://schema:passwd@127.0.0.1:1721/?service_name=aaa.jalen.com'
engine = create_engine(uri, poolclass=NullPool)
sql = "select name, age from tab_t1 where name=:name"
df = pd.read_sql_query(sql, engine, params=['jalen'])
df = pd.read_sql_query(sql, engine, params={'name': 'jalen'})
```

# 返回不改变key顺序的json数据

jsonify返回数据有时因为flask的配置问题会导致key顺序和理想的不一致

```python
data = collections.OrderedDict()
data['a'] = 'a'
data['b'] = 'b'
return make_response(jsonify(data), 200)
```

# 匹配包含某个字符串

```python
re.match('^(CHINA|CHINESE)', country_names)
```

# 执行存储过程

```python
def get_procedure_data(param1, param2, param3):
    logger.info('Run procedure and get data... ...')
    connection = engine.raw_connection()
    try:
        cursor = connection.cursor()
        cursor.callproc("get_somedata", [param1, param2])
        sql = "select * from  some_table where col=:param3"
        df = pd.read_sql_query(sql, engine, params=[param3])
        cursor.close()
        connection.commit()
        return df.to_dict(orient='records')
    except Exception as e:
        logger.error(getattr(e, 'message', repr(e)))
        logger.error(traceback.format_exc())
    finally:
        connection.close()
```

# Flask日志配置

```python
logging.basicConfig(level=logging.INFO, format="%(asctime)s %(process)d:%(thread)d %(filename)s:%(lineno)d:%(levelname)s:%(message)s")
logger = logging.getLogger(__name__)
```

# 数据库查询全字段模糊匹配并分页

```python
def serialize(model, append_columns=[]):
    if len(append_columns) > 0:
        columns = [c.key for c in class_mapper(model[0].__class__).columns]
        data = dict((c, getattr(model[0], c)) for c in columns)
        for index, column in enumerate(append_columns):
            data[column] = model[index+1]
        return data
    else:
        columns = [c.key for c in class_mapper(model.__class__).columns]
        return dict((c, getattr(model, c)) for c in columns)

def list_something():
    data = request.get_json()
    page_num = data.get('page_num')
    page_size = data.get('page_size')
    search_key = data.get('search_key')
    query = db.session.query(SomeModel)
    if search_key:
        search_key_list = search_key.split(',')
        like_text_list = ["%{}%".format(sk) for sk in search_key_list]
        like_phases = []
        name_like = [SomeModel.name.ilike(j) for j in like_text_list]
        ver_like = [SomeModel.version.ilike(j) for j in like_text_list]
        type_like = [SomeModel.type.ilike(j) for j in like_text_list]

        like_phases.extend(name_like)
        like_phases.extend(ver_like)
        like_phases.extend(type_like)
        
        query = query.filter(or_(*like_phases))
    res = query.order_by(desc(SomeModel.createtime), SomeModel.name)
    pagination = res.paginate(
        page=int(page_num),
        per_page=int(page_size),
        error_out=False)
    data_models = pagination.items
    data_list = [serialize(rep) for rep in data_models]
    return make_response(data_list, 200)
```

# Pandas批量插入

```python
d_type = {}
for i, j in zip(df.columns, df.dtypes):
    if "object" in str(j):
        d_type.update({i: VARCHAR(64)})
    elif "int" in str(j):
        d_type.update({i: INTEGER()})
    elif "date" in str(j):
        d_type.update({i: DATE()})
df.to_sql('tb_user', engine, if_exists='append', index=False, dtype=d_type)
```

# InfluxDB操作

```python
db_name = 'some_db_name'
client = InfluxDBClient(host='192.168.0.1', port=8086, database=db_name)

# delete point
params = {"db": db_name, "q": "delete from tb_some_table where id='%s'" % bid}
client.request('query', 'get', params=params)

# insert point
df["time"] = '2022-12-12 00:00:00'
client.write_points(df.to_dict(orient='records'))
```

# Flask login

```python
user = User.query.get(username)
login_user(user)
```

# 字典遍历

```python
for key, value in six.iteritems(some_dict):
    pass
```

# Pandas基本操作

```python
# 列重命名
df.rename(columns={'name1': 'name2'}, inplace=True)

# 丢弃列
df = df.drop(columns=['name1'])
df.drop(columns=['name1'], inplace=True)

# 丢弃空行
df = df.dropna(subset=['name1', 'name2'])

# 丢弃重复行
df = df[['name1', 'name2']].drop_duplicates()
df = df.drop_duplicates(subset=['name1', 'name2'], keep=False)
df.drop_duplicates(subset=['name1', 'name2'], keep='first', inplace=True)

# 列操作
df['name1'] = df.groupby(['name2', 'name3'], as_index=False)['name4'].transform(lambda x: ','.join(x))

# 合并两个df
df = pd.merge(df1, df2, how="left", on=["name1", "name2"])

# 过滤
df = df[['name1', 'name2', 'name3', 'name4']]
df = df[df.name1 == 'xxx']
df = df[df.name1 != 'xxx']
df = df[df.name1.str.contains('xxx')]
df = df[~df.name.isin(['Jalen', 'Bob', 'Kite'])]
df = df[df.timestamp > 6000]
df = df[df.name.notnull()]
df = df[df.name.isnull()]
df = df.groupby('name1').filter(lambda df: df.dependency.notnull().values.any())
df = df[(df.name.isin(['Jalen', 'Jones'])) & ~df.age.isin([12, 22, 32])]
df = df[df['name'].apply(lambda x: 'Jalen' in x)]
df = df[df.name.str.contains(f"(?i)Jalen")]
df = df[df.name.str.lower() == 'jalen']

# 填充nan
df['name1'] = df['name1'].fillna('')
df.fillna(0, inplace=True)
df.fillna('', inplace=True)
df.fillna({'age1': 0, 'age2': 0, 'name': ''}, inplace=True)

# 变换
df['name1'] = df['name2'].apply(lambda x: 'yyy' if x=='xxx' else '')
df["name1"] = df["name2"].apply(lambda x: util.get_name(x))
df['name1'] = df.apply(lambda x: 'yyy' if x['name2']=='xxx' else '', axis=1)

# 追加多个df
df = pd.concat([df1, df2], sort=False)

# 根据逗号拆分成多行
df.reset_index(drop=True, inplace=True)
df = df.drop('name', axis=1).join(df['name'].str.split(',', expand=True).stack().reset_index(level=1, drop=True).rename('name'))

# 赋值
df["name"] = df.name.str.upper()

# 获取行数
count = int(df.shape[0])
count = len(df)

# 分组聚合
df = df.groupby(by=['name'], as_index=False).size()
df = df.groupby(by=['name'], as_index=False)['value'].sum()
groups = df.groupby(['name1', 'name2', 'name3'], as_index=False)
for group in groups:
    pass
max_date = df[df.env == 'xxx']['start_date'].max()
df = df.sort_values('createtime', ascending=False).groupby(by=['name'], as_index=False).head(1)  # tail(1)

# 重置行索引
df.reset_index(drop=True, inplace=True)

# 时间列转换
df['create_time'] = pd.to_datetime(df['create_time'])

# 排序
df = df.sort_values(by="age", axis=1, ascending=False)
df.sort_values(by='similarity', ascending=False, inplace=True)
df.sort_values(by='time', axis=0, ascending=False, inplace=True)
df.sort_values(by='name', axis=0, ascending=False, inplace=True, na_position='first')

# 转秩
df = df.groupby([df['create_time'], df_m['name']], as_index=False).size().reset_index().fillna(0)
df = df.pivot_table(index='create_time', columns='name').reset_index().fillna(0)
df.columns = ['create_time' if x[0] == 'create_time' else x[1] for x in df.columns]
df.rename(index=str, columns={'create_time': 'Date'}, inplace=True)

# 发送html格式邮件
report_html = df_report.to_html(index=False, border=1, justify='center', na_rep="", escape=False)
report_html = report_html.replace('<th>', '<th style = "background-color: #e2fcfd; width:150px">')
report_html = report_html.replace('class', 'cellspacing=\"0\" class')
report_html = report_html.replace('<tbody>', '<tbody style="text-align:center">')
kwargs = {'title': title, 'receiver': receiver, 'body': body_header + normal_body + report_html + body_footer}
subject = "xxx"
util.send_email(subject, **kwargs)

# 读取excel表格
df = pd.read_excel(r"ddd.xlsx", engine='openpyxl', sheet_name='Sheet1')
df.to_excel("xxxx.xlsx", index=False, sheet_name='ddd')

# 读取csv文件
df = pd.read_csv(file_name)
df.to_csv(file_name, index=False)

# 取值
age = df.loc[df.name == 'Jalen', 'age'].values[0]
val = df.iloc[0]['value']
val = df['value'].iloc[0]

# df转字典列表
df.to_dict(orient='records')

# 转list
data = df1.append(df2).drop_duplicates(subset=['name', 'age'], keep=False)['name'].tolist()

# 逐行遍历df
for index, row in df.iterrows():
  pass

# 拼接生成新列
df['name'] = df['name1'] + '-' + df['name2']
```

# 执行Dig命令

```python
cmd = 'dig xxx -t CNAME +short'
process = subprocess.Popen(shlex.split(cmd), stdout=subprocess.PIPE)
out, err = process.communicate()
```

# xml转dict

```python
xmltodict.parse(xml_str_content)
```

# 通过哨兵连接redis

```python
def get_redis_client():
    sentinel_ip_list = ['192.168.0.1', '192.168.0.2', '192.168.0.3']
    master_name = 'mymaster'
    master_password = 'xxx'
    sentinel_port = 26379

    conf = {
        'sentinel': [(ip, sentinel_port) for ip in sentinel_ip_list],
        'master_name': master_name,
        'connection_conf': {
            'socket_timeout': 5,
            'retry_on_timeout': True,
            'socket_keepalive': True,
            'max_connections': 10,
            'db': 0,
            'encoding': 'utf8',
            'decode_responses': True,
        }
    }
    redis_sentinel = Sentinel(conf['sentinel'], **conf['connection_conf'])

    # ip, port = redis_sentinel.discover_master(master_name)
    # logger.info('master ip: %s' % ip)
    # logger.info('master port: %s' % port)
    return redis_sentinel.master_for(master_name, password=master_password)
```

# 字符串转字典

```python
data = eval(data_str)
data = json.loads(data_str)
```

# RedLock使用

```python
try:
    # Add lock to save
    lock_name = 'lock_%s' % number
    redis_client = redisutils.get_redis_client()
    save_lock = Redlock(key=lock_name, masters={redis_client}, auto_release_time=30 * 1000)
    save_lock.acquire(timeout=5)

    logger.info('Store data')
    save(body, redis_client)
    logger.info('Store end')

    save_lock.release()
except Exception as e:
    pass
```

# 事务操作

```python
connection = engine.connect()
trans = connection.begin()
try:
  sql = "update tb set age=:age where name=:name"
  connection.execute(text(sql), name=name, age=age)
  trans.commit()
except:
    trans.rollback()
    raise
finally:
    connection.close()
```

# KB查询

```python
query_body = {"query": {'multi_match': {
        'query': 'query_string',
        'fields': ['f1', 'f2', 'f3', 'f4']
    }}, 'size': 20}
query = es.search(index=index, body=query_body)
res = [q['_source'] for q in query['hits']['hits']]
```

# ISO8859数据库存非西欧字符

```python
# 存的时候
model.value = val.encode("unicode_escape").decode("utf-8") if val else ''

# 取的时候
df['value'] = df['value'].apply(lambda x: x.encode('ISO-8859-1').decode('unicode_escape') if x else '')
```

# KB主页展示表格转树状

```python
data = defaultdict(list)
```

# 字典拼接

```python
data = {'name': 'Jalen', 'age': 12}
data1 = dict(data, **{'weight': 120, 'money': 'no'})

data.update({'fat': False, 'nickname': 'cheng'})
```

# 批量插入

```python
df = df[['name1', 'name2', 'name3', 'name4']]
data_to_insert = [tuple(xi) for xi in df.values]
connection = engine.raw_connection()
cursor = connection.cursor()
cursor.executemany("insert into tb01 values (:1, :2, :3, :4)", data_to_insert)
cursor.close()
connection.commit()
```

# JSON数据排序

```python
def json_ordered(obj):
    """ ref: https://stackoverflow.com/questions/25851183/how-to-compare-two-json-
     objects-with-the-same-elements-in-a-different-order-equa """
    if isinstance(obj, dict):
        return sorted((k, json_ordered(v)) for k, v in obj.items())
    if isinstance(obj, list):
        return sorted(json_ordered(x) for x in obj)
    else:
        return obj
```

# 多层字典转为一层字典，key用点号分隔

```python
def split_dict_keys_with_point(tmp_dict, top_key_list):
    collection = []
    for k, v in tmp_dict.items():
        if type(v) != dict:
            if not top_key_list:
                collection.append({k: v})
            else:
                collection.append({f"{'.'.join(top_key_list)}.{k}": v})
        else:
            top_key_list.append(k)
            stack_res = split_dict_keys_with_point(v, top_key_list)
            top_key_list.pop()
            collection.extend(stack_res)
    return collection


if __name__ == '__main__':
    tmp_dict = {
        'index': 'some_index',
        'timestamp': '2022-12-12 00:00:00',
        'metrics': {
            'name': 'jalen',
            'age': 12,
            'love': 'eat',
            'properties': {
                'address': 'HeFei',
                'phone': '1888888888'
            }
        }
    }
    data = util.split_dict_keys_with_point(tmp_dict, [])
```

# 格式化字符串

```python
if __name__ == '__main__':
    data_list = [{
        'name': 'Jalen',
        'age': 12,
        'weight': 120,
        'address': 'HeFei'
    }, {
        'name': 'Jones',
        'age': 12,
        'weight': 120,
        'address': 'HeFei'
    }, {
        'name': 'Bob',
        'age': 12,
        'weight': 120,
        'address': 'HeiHe'
    }]
    df = pd.DataFrame(data_list)
    fields = list(df.columns.values)
    reports = df.values.tolist()
    fields_length = {}
    for i in fields:
        msg_col = df[i].tolist()
        col_width = []
        for j in msg_col:
            col_width.append(len(str(j)))
        fields_length[i] = max(col_width) if max(col_width) > 20 else 20

    markdown = ''
    report_length = len(reports)
    lens, title = [], []
    for field in fields:
        title.append(("{:<%d}" % fields_length[field]).format(field))
        lens.append(fields_length[field])
    split_line = "+%s+" % '+'.join(["-" * i for i in lens])
    title = "|%s|" % "|".join(title)
    keep_split_line = 1
    head_msg = "%s  \n%s  \n%s  \n" % (split_line, title, split_line)
    msg = "\n%s" % head_msg
    for count, report in enumerate(reports):
        row = "|%s|" % "|".join([("{:<%d}" % lens[i]).format(str(item)) for i, item in enumerate(report)])
        msg = msg + row + "  \n" + split_line + "  \n" if keep_split_line else msg + row + "  \n"
        if report_length == count + 1:
            print(msg)
    print('End')
```
