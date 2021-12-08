#tool implement process

![image](https://github.com/ClorisUcas/A-scenario-description-language/blob/main/documents/pictures/process_flowchart.png)

1. 利用paser编译语言的脚本文件，输出json数据
2. 解析map file，抽象出map接口方便后续的使用。接口包括两种，一种是获取地图中的元素，一种是计算车辆轨迹线。比如车辆的turn_left行为实际上是利用map接口获取车辆行驶的具体轨迹。
3. 调用map和simulator接口创建初始场景，通过pygame显示出来
4. 构造行为树，并执行行为树
5. 判断是否满足退出条件，如果满足就结束模拟
6. 如果不满足退出条件，更新行为树的状态
7. 执行控制PID算法计算车辆的油门，刹车等具体数值
8. 调用模拟器和地图接口，实时显示车辆运动的轨迹
9. 在固定的时间间隔获取智能体的相关数据，判断是否满足退出条件