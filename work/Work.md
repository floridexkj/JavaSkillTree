�����ǹ�������������

����b2b��Ŀ��ʱ����Ҫ��Debug Configuration��ǩ ��� VM options����Ϊ��
-server -Xverify:none -Xms1024M -Xmx2048M -XX:+UseParNewGC -XX:PermSize=128m -ea

�ҵ����ϲ����Ľ������£�

-server ʹ��server jvm������ʹ�ã���Щdoc˵IDEA�Ӹ�ѡ���������ٶȡ� 
-Xverify��none �ر�Java�ֽ�����֤���Ӷ��ӿ�����װ����ٶȣ���ʹ���ڽ�Ϊ��֤Ŀ�Ķ������Ĺ���������װ���࣬����������ʱ�䡣

Xms1024m ������ڴ��ʼֵΪ1024MB
Xmx2048m ������ڴ����ֵΪ2048MB

-XX:+UseParNewGC��
ʹ��-XX:+UseParNewGCѡ����������������ʹ��ParNew GC���ԣ����ϴ�Ĭ��ʹ��Serial Old GC���ԡ�
ParNew�ռ�����ʵ����Serial�ռ����Ķ��̰߳汾�����������У���������У������������㷨����������-ѹ��

-XX:PermSize=128m
JVMʹ��-XX:PermSize���÷Ƕ��ڴ��ʼֵ��Ĭ���������ڴ��1/64����XX:MaxPermSize�������Ƕ��ڴ�Ĵ�С��Ĭ���������ڴ��1/4��
