# 高精地图API #
## 地图加载 ##
| 方法 | 参数 | 解释 
|:-----|:---- |:----
|Hdmap(path)|path：string，地图文件路径|加载高精地图文件
|get_map()| |获取高精地图数据
|get_local_map(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部地图数据


## 地图元素操作 ##

### 根据ID获取地图元素 ###
| 方法 | 参数 | 解释
|:-----|:---- |:----
|get_lane_by_id(id)|id：string|获取地图中标识号为id的车道
|get_junction_by_id(id)|id：string|获取地图中标识号为id的路口
|get_signal_by_id(id)|id：string|获取地图中标识号为id的信号灯
|get_crosswalk_by_id(id)|id：string|获取地图中标识号为id的人行横道
|get_stop_sign_by_id(id)|id：string|获取地图中标识号为id的停车标志
|get_yield_sign_by_id(id)|id：string|获取地图中标识号为id的让行标志
|get_clear_area_by_id(id)|id：string|获取地图中标识号为id的ClearArea
|get_speed_bump_by_id(id)|id：string|获取地图中标识号为id的减速带
|get_overlap_by_id(id)|id：string|获取地图中标识号为id的Overlap
|get_crosswalk_by_id(id)|id：string|获取地图中标识号为id的人行横道
|get_road_by_id(id)|id：string|获取地图中标识号为id的道路
|get_parking_space_by_id(id)|id：string|获取地图中标识号为id的停车区域

### 单独获得Road属性 ###
| 方法 | 参数 | 解释
|:-----|:---- |:----
|get_road_type(road_id)|road_id：需要获得类型的道路ID|获得道路类型，以 num1_way_num2_lane_num3_positive_direction 表示是单/双向、含有几条车道、几条同方向lane
|get_road_by_lane_count(lane_count)|lane_count：车道数量|获得含有lane_count个车道的道路ID列表


### 获取临近地图元素 ###
| 方法 | 参数 | 解释
|:-----|:---- |:----
|get_nearest_lane(x,y,z)|x,y,z：中心点坐标|获取地图中距离点(x,y,z)最近的车道
|get_lanes(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有车道
|get_roads(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有道路
|get_junctions(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有路口
|get_signals(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有信号灯
|get_crosswalks(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有人行横道
|get_stop_signs(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有停车标志
|get_clear_areas(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有ClearArea
|get_speed_bumps(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有减速带
|get_parking_spaces(x,y,z,distance)|x,y,z：中心点坐标；distance：半径|获取地图中以参考点(x,y,z)为中心，半径distance的局部区域中的所有停车区域
|get_nearest_lane_with_heading (x,y,z,distance,central_heading,max_heading_difference)|x,y,z：中心点坐标；distance：半径，central_heading：中心朝向，max_heading_difference：最大朝向差|判断车辆姿态，获取在(x,y,z)为中心，半径distance的局部区域中最近的车道
|get_lanes_with_heading (x,y,z,distance,central_heading,max_heading_difference)|x,y,z：中心点坐标；distance：半径，central_heading：中心朝向，max_heading_difference：最大朝向差|判断车辆姿态，获取在(x,y,z)为中心，半径distance的局部区域中最近的车道

### 路径规划 ###
| 方法 | 参数 | 解释
|:-----|:---- |:----
|planning(x,y,z,distance)|x,y,z：中心点坐标；distance：距离|获得从点(x,y,z)出发，长度distance的路径点列
|left_lane_turn(location,sin,distance)|location，起点坐标； sin，判断转弯结束的正弦值； distance，转弯结束后继续行驶的距离|获得从点（x,y,z）出发左转的路径点列
|right_lane_turn(location,sin,distance)|location，起点坐标； sin，判断转弯结束的正弦值； distance，转弯结束后继续行驶的距离|获得从点（x,y,z）出发右转的路径点列
|left_lane_change(location,distance)|location，起点坐标； distance，转弯结束后继续行驶的距离|获得从点（x,y,z）出发向左变道的路径点列
|right_lane_change(location,distance)|location，起点坐标； distance，转弯结束后继续行驶的距离|获得从点（x,y,z）出发向右变道的路径点列
|get_planning(x1,y1,z1,x2,y2,z2)|x1,y1,z1：起点的三坐标；x2,y2,z2：终点的三坐标|获得从起点到终点的轨迹点列
|get_way_planning(point1, point2)|point1：起点，PointENU对象；point2：终点，PointENU对象|获得从起点到终点的轨迹点列