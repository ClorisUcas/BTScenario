#类属性和类方法#

##1. 通用方法 ##

| 方法 | 解释 | 实现
|:-----|:----|:----
|distance(object) | 计算与object之间的距离 | y
|near_distance(object) | 如果object是单个智能体，计算与object之间最近的距离；如果是流，则计算流中最近的车辆和距离 | n
|setPosition(map)| 设置物体的位置 | y
|setRelativePosition(heading, relativeto, left_distance, front_distance, right_distance)|设置相对位置， heading 物体的朝向； relativeti：相对的物体，left_distance与相对物体左侧距离 | y
|getPosition()| 获取物体的位置 | y


##2 Car/Aut_Car

###属性###

| 属性 | 解释 | 实现
|:-----|:---- |:----
|speed|车辆速度 | y
|color|车辆颜色 | y
|type| small_car/compact_car/medium_car/luxury_car/van/heavy_truck/semi_trailer/trailer/bus/tram/train/wheelchair/micro_scooter/scooter | n
|weight |车载重量 | n
|realwight|实际重量 | n
|front_fog_lamp|前雾灯 on/off | n
|rear_fog_lamp |后雾灯 on/off | n
|fog_lamp | 雾灯 on/off | n
|head_lamp | 前照灯 | n
|low_beam | 近光灯 | n
|high_beam | 远光灯 | n
|low_high_beam | 交替远近光灯 | n
|reversing_light | 倒车灯 | n
|stop_lamp | 制动、刹车灯 | n
| emergency_flashers | 双闪 | n
|clearance_lamp | 示廓灯 | n
|tail_lamp | 尾灯 | n
|parking_lamp | 前位置灯 | n



###方法###


| 获取状态 | 参数 | 解释 |实现
|:-----|:---- |:---- |:----
|isCollided()| 无 |是否发生碰撞 | y
|is_keep_lane()| 无 |是否保持车道 | n
|is_change_lane()| 无 |是否变道 | n
|is_wrong_direction()| 无 |是否逆行 | y
|is_run_red_light()| 无 |是否闯红灯 | n
|is_turn()| 无 |是否转弯 | y
|is_turn_left()| 无 |是否左转弯 | y
|is_turn_right()| 无 |是否右转弯 | y
|is_turn_u()| 无 |是否转U型弯 | n
|is_run()| 无 |是否运行 | y
|is_start()| 无 |是否启动 | n
|is_illegal_parking()| 无 |是否违法停车 | n
|is_anchor()| 无 |是否抛锚 | n
|is_overtake()| 无 |是否超车 | n
|is_follow(object)| 跟随的车辆 |是否跟车 | n
|is_tailgating()| 无 |是否追尾 | n
|is_cut_off(object)| 无 |是否加塞 | n
|is_overweight()| 无 |是否超重 | n


| 车辆在结构化的道路行为 | 参数 | 解释 | 实现
|:-----|:---- |:---- |:----
|cruise(targetSpeed)| 目标速度 |保持车速 | y
|stop()| 无 |停车 | y
|brake()| 无 |刹车 | y
|reverse()| 无 |倒车 | y
|change_lane(direction)|  left、right |变道 | y
|turn_u()| 无 |转U型弯| n
|turn(direction)| left、right |左转 | y
|go_straight()| 无 |直行 | y
|keep_lane()| 无 |保持车道 、follow_lane | y
|anchor()| 无 |抛锚 | n
|overtake()| 无 |超车 | n
|follow(object)|  |跟随object运动 | n
|tailgate(distance)|  |追尾 distance表示追尾距离 | n
|cut_off(object)|  |加塞 | n


| 车辆在非结构化道路的行为  | 参数 | 解释 | 实现
|:-----|:---- |:---- |:----
|follow_route(formula,x,y)| |跟着公式路线形式，坐标原点在被测车辆初始位置，x，y可以设置区间范围 | n
|stop()| |停车|y
|brake()| |刹车|y
|reverse(formula,x,y)| |倒车|y
|anchor()| |抛锚 | n
|overtake()| |超车 | n
|follow(object)| |跟随object运动 | n
|tailgate(distance)| |追尾 distance表示追尾距离 | n
|cut_off(object)| |加塞 | n

##3. Car_Flow ##


###属性###

| 属性 | 解释 | 实现
|:-----|:---- |:----
|car_distance| 车辆之间的间隔 number/[minnumber，max_number] | y
|number| 车流中车辆的数量 | y
|speed|车辆速度 | y
|color|车辆颜色 | y


###方法###
同Car



##4. Pedestrian ##


###属性###

| 属性 | 参数 | 解释 | 实现
|:-----|:---- |:---- |:----
|speed| |行人的速度 | y


###方法###

| 行人行为 | 参数 | 解释 | 实现
|:-----|:---- |:---- |:----
|cross()| | 横跨马路 | y
|walkalong()| | 沿着道路行走 | n



##5. Pedestrian_Flow ##



###属性###

| 属性 | 解释 |实现
|:-----|:----|:----
|speed| 速度 | y
|pedestrian_distance| 行人之间的间隔 | y
|number| 行人的数量| y


###方法###
同pedestrian


##6. Environment ##

###属性###

| 属性 | 解释
|:-----|:----
|type| /'ClearNoon'/'ClearSunset'/'CloudyNoon'/'CloudySunset'/'Default'/'HardRainNoon'/'HardRainSunset'/'MidRainSunset'/'MidRainyNoon'/'SoftRainNoon'/'SoftRainSunset'/'WetCloudyNoon'/'WetCloudySunset'/'WetNoon'/'WetSunset'
|cloudiness   | float(0~100) 
|precipitation   | float(0~100) 
|precipitation_deposits   | float(0~100)  
|wind_intensity  |  float(0~100) 
|sun_azimuth_angle   | float(0~100) 
|sun_altitude_angle   |  float(0~100)    






##7. TemporaryArea ##

###属性###

| 属性 | 解释
|:-----|:----
|type| 临时场所的类型 'road_works'


###方法###


##8. Obstacle ##

###属性###

| 属性 | 解释
|:-----|:----
|type| "static"/"dynamic"静止的障碍物，运动的障碍物
|length| 长
|width| 宽
|height| 高

###方法###

| 获取属性 | 参数 | 解释
|:-----|:---- |:----
|trajectory()|  |移动轨迹




#MAP#

##1.Waters ##

| 属性 | 解释
|:-----|:----
|speed| 水流速度
|length| 长
|width| 宽
|height| 深度
|area| 面积


##2.Land ##


| 属性 | 解释
|:-----|:----
|area| 面积
|length| 长
|width| 宽
|type| desert/gobi/mountains/grassland/graval_land 路面类型
|bulk_density | 土壤密度/砂石密度
|scope_range | 坡度范围



##3.Sky ##

| 属性 | 解释
|:-----|:----
|area| 面积
|length| 长
|width| 宽
