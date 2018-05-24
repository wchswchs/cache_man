# cache_man
Cache框架（支持关联Cache更新）

例如：

现存在两个key

key1是show_detail_1，缓存id为1的演出详情<br/>
key2是hot_shows，缓存热门演出列表，其中id为1的演出也是热门演出

此时，如果show_detail_1更新，hot_shows也同时更新

## 特性：

1. 兼容Spring Cache @Cacheable，@CachePut，@CacheEvict注解
2. 支持关联缓存更新
3. 支持缓存自动过期

## 用法

### cache配置文件说明 ###
* cacheName: 全局cache名，对应redis一级目录
* maxEvictThreadNum: 删除缓存最大线程数，即最多开启多少个线程批量删除关联key
* batchEvictThreadPoolSize: 删除缓存线程池大小
* defaultExpiredTime: 默认过期时间

### 使用示例 ###

```java
    @JobsCacheable(domain = "'show'", key = "'show_detail_'+#id")
    public Object getShowInfoForRec(String id) {
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(id);
        methodShow.setName("李四");

        return methodShow;
    }

    @JobsCacheEvict(domain = "'show'", key = "'show_detail_'+#id")
    public Object updateShowForRecommend(String id, String name) {
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(id);
        methodShow.setName(name);

        return id;
    }

    @JobsCacheable(domain = "'show'", key = "'recommend_shows'", expireTime = 5000L)
    public Object getRecommandShows(String id, String name) {
        List<ShowInfo> list = new ArrayList<ShowInfo>();
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(id);
        methodShow.setName(name);
        list.add(methodShow);
        return list;
    }

    @JobsCacheable(key = "'order_detail_'+#id")
    public Object getCacheableShowInfoForRecByAnnotation(String id) {
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(id);
        methodShow.setName("李四");
        return methodShow;
    }

    @JobsCacheable(key = "'recommend_orders'")
    public Object getCacheableRecommandShows(String id, String name) {
        List<ShowInfo> list = new ArrayList<ShowInfo>();//getCacheableRecommandShows
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(id);
        methodShow.setName(name);
        list.add(methodShow);
        return list;
    }

    @JobsCacheEvict(key = "'order_detail_'+#id")
    public Object getCacheableUpdateShowForRecommend(String id, String name) {
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(id);
        methodShow.setName(name);
        return id;
    }
    
    @JobsCachePut(key = "'seller_detail_'+#id")
    public Object getsCachePutGetShowInfoForRecByAnnotation(String var1) {
        ShowInfo methodShow = new ShowInfo();
        methodShow.setId(var1);
        methodShow.setName("赵六");
        return var1;
    }
    
