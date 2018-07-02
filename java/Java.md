# jvm的笔记

  p140页
  
  
  在HashMap<Long,Long>的结构中,只有Key和Value所存放的两个长整型数据是有效数据,共16B(2*8B).这两个长整型数据包装成java.lang.Long对象之后,就分别
  具有8B的MarkWord(字节对象头),8B的Klass指针(字节对象指针),在加8B存储数据的long值(数据区).在这两个Long对象组成Map.Entry之后,又多了16B的对象头,
  然后一个8B的next字段和4B的int型的hash字段,为了对齐,还必须添加4B的空白填充,最后还有HashMap中对这个Entry的8B的引用,这样增加两个长整型数字,实际耗费
  的内存为(Long(24B)*2)+Entry(32B) + HashMap Ref(8B) = 88B,空间效率为16B/88B = 18%.
