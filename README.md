# d031_weibo_bert
D031 vue+django+BERT微博舆情分析系统 | 深度学习 | 话题分析

> 完整源码请加 微信： maimaidashuju  注明:git来的
> 

编号： D031 
架构：vue+django+BERT+MySQL
功能： 微博信息爬取、BERT情感分析、基于负面、中性、消极内容舆情分析、可视化分析、舆情预测算法、舆情评估、关键词分析、数据大屏等
包含开题和1W字论文。
**可爬取最新数据**
## 视频


### 数据爬取部分
用过scrapy爬虫爬取微博的最新数据，获取数据之后可以通过BERT模型对内容进行情感分析。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/123ba231b36f47d1a67a24988b795cdb.jpeg)
python 微博爬虫
🟠 爬取微博主题、文章和评论；
🟠 基于BERT的情感分析模型，可以分析微博内容的积极、消极倾向以及概率；
🟠 重复性处理、存储MYSQL；
## 二、文档
系统包含了丰富的文档，包含了开题、LW材料，直接可以参照代码进行使用。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fb90168b580e44738b2e29299995ca1e.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ddeefde9699146b5be8878d5da172733.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/eda98a47f7fb4bd49777f7bc9bd8ec79.png)


## 三、功能说明
功能模块图，系统含有2个界面
一个界面是数据大屏
另一个界面是系统的操作界面
### 数据大屏
数据大屏展示微博系统各个方面的数据的运行情况。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/553e5221780f4631b4b5be65ba9e5644.png)
### 1话题搜索
可以按照情感倾向的积极性和消极性来进行话题的搜索；
以卡片方式展示，点击卡片可以查看评论明细。
可以查看微博话题评论内容的明细，带有情感分析情感，并且可以根据IP属地进行筛选。
积极的用绿色向上大拇指表示，消极用红色向下大拇指表示，中性为黄色的人脸，显示非常的直观。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/17183d5f9f0e4c5cbb4f835e1b159321.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7e1cdf7c2012402989eb18c12abc4968.png)
### 2数据分析
数据统计、微博转发分析、舆情总览地图、微博用户分析、话题点赞热度分析、话题评论热度分析
分别使用echarts 的折线图、饼图、中国地图、柱状图、环图、动态滚动柱状图、动态滚动柱状图来实现。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3bb17476d5574e23aee5f8fcae5cb817.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/89705f1fee86474f95a5ae5451cc7ad0.png)
### 3舆情监测
采用echarts来实现可视化，分别如下：
散点图（正面和负面微博话题在散点图上展示，不同颜色展示总体积极和消极的话题，X轴和Y轴代表话题的评论和点赞数）、花瓣图（分析微博内容的IP地址归属）、旭日图情感分析（对所有话题及其情感分析倾向绘制一个旭日图）、日历图热点话题跟踪（将微博评论数热度投射到日历图上，用不同颜色深度表示话题热度）
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8f4f6f4be097490cb7cf1fd661b7ecd7.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/89642c67616e4e2d96e8be6c4dd4a9a4.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d673c52da288432f91050afd352aa2f2.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/01bfbfada9f54e9e9876325a54767894.png)



```python
 @action(methods=['get'], detail=False, url_path="getAreaChart")
    def getAreaChart(self, request):
        topic_name = self.request.query_params.get('topic')

        # 获取评论数数据
        sql_comments = (f"SELECT DATE_FORMAT(created_at, '%Y-%m-%d') AS name, SUM(comments_count) AS value "
                        f"FROM tb_weibo WHERE topics = '{topic_name}' "
                        f"GROUP BY DATE_FORMAT(created_at, '%Y-%m-%d') "
                        f"ORDER BY DATE_FORMAT(created_at, '%Y-%m-%d') ASC")
        comments_data = get_datas(sql_comments)

        # 获取转发数数据
        sql_reposts = (f"SELECT DATE_FORMAT(created_at, '%Y-%m-%d') AS name, SUM(reposts_count) AS value "
                       f"FROM tb_weibo WHERE topics = '{topic_name}' "
                       f"GROUP BY DATE_FORMAT(created_at, '%Y-%m-%d') "
                       f"ORDER BY DATE_FORMAT(created_at, '%Y-%m-%d') ASC")
        reposts_data = get_datas(sql_reposts)

        # 获取点赞数数据
        sql_attitudes = (f"SELECT DATE_FORMAT(created_at, '%Y-%m-%d') AS name, SUM(attitudes_count) AS value "
                         f"FROM tb_weibo WHERE topics = '{topic_name}' "
                         f"GROUP BY DATE_FORMAT(created_at, '%Y-%m-%d') "
                         f"ORDER BY DATE_FORMAT(created_at, '%Y-%m-%d') ASC")
        attitudes_data = get_datas(sql_attitudes)

        # 转换日期和数值数据
        def prepare_data(data):
            dates = [datetime.strptime(item['name'], '%Y-%m-%d') for item in data]
            values = [item['value'] for item in data]
            return dates, values

        dates_comments, comments_values = prepare_data(comments_data)
        dates_reposts, reposts_values = prepare_data(reposts_data)
        dates_attitudes, attitudes_values = prepare_data(attitudes_data)

        # 使用多项式回归模型进行预测
        def predict_next_3_days(dates, values):
            # 将日期转换为整数值，方便进行回归分析
            date_integers = [(date - min(dates)).days for date in dates]

            # 多项式回归
            poly = PolynomialFeatures(degree=2)
            X_poly = poly.fit_transform(np.array(date_integers).reshape(-1, 1))
            model = LinearRegression()
            model.fit(X_poly, values)

            # 预测未来3天的数据
            future_values = model.predict(poly.transform(np.array(range(len(dates), len(dates) + 3)).reshape(-1, 1)))
            # 确保预测值不小于 0
            future_values = [max(0, value) for value in future_values]

            return future_values

        # 预测评论数、转发数和点赞数
        future_comments = predict_next_3_days(dates_comments, comments_values)
        future_reposts = predict_next_3_days(dates_reposts, reposts_values)
        future_attitudes = predict_next_3_days(dates_attitudes, attitudes_values)

        # 构建返回数据
        def build_response_data(dates, values, future_values):
            # 返回原始数据加上预测值，命名为预测1、预测2、预测3
            response_data = []
            for i, date in enumerate(dates):
                response_data.append({'name': date.strftime('%Y-%m-%d'), 'value': values[i]})

            # 将预测结果加入到返回数据中
            response_data.append({'name': '预测1', 'value': future_values[0]})
            response_data.append({'name': '预测2', 'value': future_values[1]})
            response_data.append({'name': '预测3', 'value': future_values[2]})

            return response_data

        # 获取返回的评论数、转发数和点赞数数据
        response_comments = build_response_data(dates_comments, comments_values, future_comments)
        response_reposts = build_response_data(dates_reposts, reposts_values, future_reposts)
        response_attitudes = build_response_data(dates_attitudes, attitudes_values, future_attitudes)

        # 使用序列化器将数据转化为适合返回的格式
        serializer_comments = ChartSerializer(response_comments, many=True)
        serializer_reposts = ChartSerializer(response_reposts, many=True)
        serializer_attitudes = ChartSerializer(response_attitudes, many=True)

        return APIResponse(data1=serializer_comments.data, data2=serializer_reposts.data,
                           data3=serializer_attitudes.data)
```

### 4词频分析
利用jieba分析分析话题的内容词频，利用echarts-wordcloud组件来实现词云的可视化展示
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/db262fdac75840328a2fbc097e908068.png)
观点提取
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7db198a7aba64bf0b21e64793e19e9a0.png)



### 5热词分析
针对不同话题可以进行话题观点提取、关键词词频分析（分textrank和tf-idf）、通过echarts图形来进行直观的显示，并且用户可以点击话题卡片来切换需要分析的话题，图形数据会随着用户的操作而切换
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/64675e23f02c4d8b8beef0ed7775e68e.png)
### 6舆情预测
根据微博话题的评论数等数据和日期进行分析，进行图形可视化，可以查看某个话题目前的发展趋势，从而辅助用户进行决策。**这部分使用了预测算法。**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bd3cea66874a447eb8e50784b4e2c82c.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/891652623040442591c42ddbc08e610b.png)
### 7舆情评估
根据负面评论数评估舆情事件的严重性。
根据这个新的公式考虑了评论的情感倾向（经过情感分析）和用户的参与程度，可以更全面地反映数据的可信度。情感因子反映了评论的质量，而参与度因子反映了用户的积极性。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bc6d8c42a18948d6a325946bdfcb5634.png)

## 8 登录与注册
系统主要分为管理员和普通用户两个角色，管理员可以进行用户管理，修改用户可以查看的菜单范围，通过这种方式来进行访问控制，一般来说用户使用的就是系统的业务功能。
动态登录和注册界面
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6ff27f94f25c4a8da8e23af2226bf2ac.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c16fb08a8a7f4874b9d1a6bc2d2ecde9.png)

## 9 用户管理
管理员可以实现用户管理
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a6bd2f1221f746b78689e496ad6e48e3.png)

