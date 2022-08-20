---
title: python表格打印
date: 2021-11-05 14:34:43
tags: python
categories:
- [学习, Python语言学习, Python]
---

> python 打印一个表格，并输出到控制台，表格某一列要分行（这里是根据逗号分行）

# 代码

```python
def draw_table_in_webex_teams_with_comments(df):
    df.reset_index(drop=True, inplace=True)
    df_uuid = pd.DataFrame([{'uuid': str(uuid.uuid1())} for i in range(df.shape[0])])
    df.insert(0, 'uuid', df_uuid)
    df = df.drop('comments', axis=1).\
        join(df['comments'].str.split(',', expand=True).stack().
             reset_index(level=1, drop=True).rename('comments'))
    df['comments'] = df['comments'].apply(lambda x: x.strip())
    measurer = np.vectorize(len)
    columns_with_length = dict(zip(df, measurer(df.values.astype(str)).max(axis=0)))
    columns_with_length.pop('uuid')
    column_names, column_max_length = columns_with_length.keys(), columns_with_length.values()
    column_max_length = [int(le) if le > 20 else 20 for le in column_max_length]
    columns_with_length = dict(zip(column_names, column_max_length))
    comments_index = list(column_names).index('comments')

    split_line = "+%s+" % '+'.join(["-" * i for i in column_max_length])
    table_titles = "|%s|" % "|".join([("{:<%d}" % v).format(k) for k, v in columns_with_length.items()])
    message = "%s  \n%s  \n%s" % (split_line, table_titles, split_line)

    groups = df.groupby('uuid', as_index=False)
    for group in groups:
        df_item = group[1]
        df_item.drop(columns=['uuid'], inplace=True)
        row_values = df_item.values.tolist()
        display_first_row = True
        for row in row_values:
            if display_first_row:
                fill_values = [("{:<%d}" % column_max_length[i]).format(str(item)) for i, item in enumerate(row)]
            else:
                fill_values = [("{:<%d}" % column_max_length[i]).format('') if i != comments_index
                               else ("{:<%d}" % column_max_length[i]).format(str(item)) for i, item in enumerate(row)]
            row = "|%s|" % "|".join(fill_values)
            message = message + '  \n' + row
            display_first_row = False
        message = message + '  \n' + split_line
    print('```  \n%s  \n```' % message)


if __name__ == '__main__':
    data = [{'time': '2021-10-29 02:53:12',
             'name': '1111111111111',
             'value': '1',
             'comments': 'aaaaaaaaaaa, bbbbbbbbbbbb, cccccccc, dddddddddd, eeeeeeeee'},
            {'time': '2021-10-29 02:53:12',
             'name': '2222222222222222',
             'value': '1',
             'comments': 'aaaaaaaaaaa, bbbbbbbbbbbb, cccccccc, dddddddddd, eeeeeeeee'},
            {'time': '2021-10-29 02:53:12',
             'name': '333333333333',
             'value': '1',
             'comments': 'aaaaaaaaaaa, bbbbbbbbbbbb, cccccccc, dddddddddd, eeeeeeeee'}]
    df = pd.DataFrame(data)
    draw_table_in_webex_teams_with_comments(df)
    print('End!')
```

# 输出效果

```  
+--------------------+--------------------+--------------------+--------------------+  
|time                |name                |value               |comments            |  
+--------------------+--------------------+--------------------+--------------------+  
|2021-10-29 02:53:12 |1111111111111       |1                   |aaaaaaaaaaa         |  
|                    |                    |                    |bbbbbbbbbbbb        |  
|                    |                    |                    |cccccccc            |  
|                    |                    |                    |dddddddddd          |  
|                    |                    |                    |eeeeeeeee           |  
+--------------------+--------------------+--------------------+--------------------+  
|2021-10-29 02:53:12 |2222222222222222    |1                   |aaaaaaaaaaa         |  
|                    |                    |                    |bbbbbbbbbbbb        |  
|                    |                    |                    |cccccccc            |  
|                    |                    |                    |dddddddddd          |  
|                    |                    |                    |eeeeeeeee           |  
+--------------------+--------------------+--------------------+--------------------+  
|2021-10-29 02:53:12 |333333333333        |1                   |aaaaaaaaaaa         |  
|                    |                    |                    |bbbbbbbbbbbb        |  
|                    |                    |                    |cccccccc            |  
|                    |                    |                    |dddddddddd          |  
|                    |                    |                    |eeeeeeeee           |  
+--------------------+--------------------+--------------------+--------------------+  
```
