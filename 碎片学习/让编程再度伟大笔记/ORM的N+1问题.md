在所有的ORM中，这都会是一个问题，新手很容易踩到坑。进而导致系统变慢，然后拖垮整个系统

还是拿代码来说事，上篇我们定义了一个User的模型，这次还继续沿用，然后增加一个Post（文章）的模型。User和Post是一对多的关系，也就是User是Post的外键。代码如下:

假设我们有这样的代码，现在系统里面有十个用户，每个用户写了一篇文章，也就是十篇文章。

接下来我们有一个需求，展示一个文章列表页，列表页上展示的信息包括：文章标题，文章作者名称。就这两个字段，也不需要分页。

```
posts = Post.objects.all()  #  获取所有的文章数据，注意此时不会执行sql语句  by the5fire
result = []
for post in posts:   # 此时会执行select * from post的查询
    result.append({
        'title': post.title,
        'owner': post.owner.name,  # 此时会执行  select * from user where user_id = <post.user_id>
    })
```

每次循环都要查一下user表，也就是说，如果我第一次查询是10条记录，那么最终我需要执行的查询语句就是10 + 1 = 11条语句。如果我第一次查询出来的是N条记录，那么最终需要执行的sql语句就是N+1次。

这就是N+1的问题。

但是如果懂SQL的话，就知道，其实这就是一个简单的JOIN语句。一条语句就能查出所有的数据，搞什么N+1.