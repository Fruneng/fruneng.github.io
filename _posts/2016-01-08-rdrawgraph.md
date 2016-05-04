---
layout: post
title: "R统计绘图"
description: ""
category: R
tags: [R]
---

#### R简介

R是做什么的?

R是一个有着统计分析功能及强大作图功能的软件系统,是由新西兰奥克兰大学统计学系。

目前，我只是简单的使用R处理数据库后台数据输出一些报表。


github:
{% include icon-github.html username="fruneng" %}/
[strategy_performance.R](https://gist.github.com/Fruneng/8925e61f41fceb3f1a73)

这个例子展示了如何从数据库中读取数据，并使用R简单的处理后，输出图表

{% highlight R %}
library('RMySQL')

lss_log.strategy_performance <- function(con){
    sp<-dbGetQuery(con,"select date_format(log_date, '%Y-%m-%d') as log_date, sum(encrypt_count) as encrypt_count, sum(decrypt_count) as decrypt_count from strategy_performance group by log_date order by log_date desc limit 0, 10")

    if (nrow(sp)>0){
        sp<-sp[order(sp$log_date),]

        date<-sp$log_date

        m_sp<-as.matrix(sp[c("encrypt_count", "decrypt_count")])

        rownames(m_sp) <- date

        m_sp<-t(m_sp)

        barplot(m_sp,
            names.arg = sp$log_date,
            main = "strategy_performance",
            sub = "nearly 10 day's data",
            xlab = "date", ylab = "count",
            col = c("blue","green"),
            legend = rownames(m_sp),
            beside = TRUE)
    }
}

lss_log.encrypt_performance <- function(con){
    yesterday <- Sys.Date()-1

    sql <- paste("select strategy_id, encrypt_count from strategy_performance where log_date='",
        format(yesterday, format = "%Y-%m-%d"), "'", sep = "")

    sp <- dbGetQuery(con, sql)

    sp <- sp[which(sp$encrypt_count>0),]

    if (nrow(sp)>0){
        count <- sp$encrypt_count
        lbs <- paste("id:",sp$strategy_id," c:",sp$encrypt_count,sep="")

        pie(count, labels = lbs,
            main = "yesterday strategy encrypt performance")
    } else {

    }
}

lss_log.decrypt_performance <- function(con){
    yesterday <- Sys.Date()-1

    sql <- paste("select strategy_id, decrypt_count from strategy_performance where log_date='", 
        format(yesterday, format = "%Y-%m-%d"), "'", sep = "")

    sp <- dbGetQuery(con, sql)

    sp <- sp[which(sp$decrypt_count>0),]

    if (nrow(sp)>0){
        count <- sp$decrypt_count
        lbs <- paste("id: ", sp$strategy_id," c:", sp$decrypt_count, sep = "")

        pie(count, labels = lbs,
            main = "yesterday strategy decrypt performance")
    } else {

    }
}


main <- function(){
    con <- dbConnect(MySQL(), 
        dbname = "******",
        username = "******",
        password = "******",
        host = "******",
        port = 3306);

    pdf("strategy_performance.pdf", width = 25, height = 25)

    lss_log.strategy_performance(con)
    lss_log.encrypt_performance(con)
    lss_log.decrypt_performance(con)

    dev.off()

    dbDisconnect(con)
}

main()






{% endhighlight %}
