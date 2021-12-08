## 数据类型 ##
	map_object_name:= cname
	scenario_name := cname 
	suite_name := cname
	object_name :=cname
	event_name :=cname
	object := cname
    object_property := cname
	min_time := number
    max_time := number
	swarm_number := "[" number "]"
	interval_defn := ("[" [min_time] ":=" max_time "]") | ("["  min_time ":=" [max_time] "]" )
	
	join_operator:= JOIN_OPERATOR
	JOIN_OPERATOR:= "&&" | "||"
	judge_signal:= JUDGE_SIGNAL
	JUDGE_SIGNAL:=  "==" | ("!" "=")  | ("<" "=")| (">" "=") | "<" | ">"
	equation_signal:= EQUATION_SIGNAL
	EQUATION_SIGNAL:=  "=" | ("!" "=")  | ("<" "=")| (">" "=") | "<" | ">"
	left_braket := LEFT_BRAKET
    LEFT_BRAKET := "("
    right_braket := RIGHT_BRAKET
    RIGHT_BRAKET := ")"
    not := "!"
    
   
	boolean := true | false 
    c_value:= value
    value:= dict
          | list
          | string
          | number
          | true
          | false
          | null  
    

    true:= TRUE
    false := FALSE
    null:= NULL
    TRUE := "true" | "True" | "TRUE"
    FALSE := "false" | "FALSE" | "False"
    NULL := "null" | "NULL" | "Null"
    list := "[" [value ("," value)*] "]"
    dict := "{" [pair ("," pair)*] "}"
    pair := (string |number )":=" value
	

## 语言架构 ##
**定义**

	<scenario_defn>
	[suite_defn]
	main_defn

**说明**

语言整体架构包括测试用例，测试套件，和main模块入口三个部分，可以不用测试套件，在main_defn中直接调用


###测试用例###

**定义**


	scenario_defn := "scenario" scenario_name "(" [params] ")" "{" map_defn init_defn execute_defn oracle_defn "}"
    params:= dict
	
	
**描述**：                                           

测试用例（一个实际的scenario场景描述）分为四个部分，静态地图场景的定义map_defn，交通参与者的初始化init_defn，动态场景的描述execute_defn，以及评测标准oracle_defn。每一个scenario在场景生成时会自动创建一个Scenario内置类。

**说明**：
	
1.`<params>`表示传入的参数,是一个字典类型,在Scenario类中会对dict进行处理，使得可以直接用`params.road_number`
	
	scenario turnLFlow(params){
		map{
			Road r1 with color = params.color;
		}
		...
	}
	main{
		loadMap('town.mif');
		params = {'color' := 'red' };
    	turnLFlow(params);
	}

2.定义一个没有传入参数的test的`scenario`

	scenario test(){
		map{
			Road r1 with color = 'red';
		}
		init{}
		execute{}
		oracle{}
	}
	
####测试用例中地图场景描述####

**定义**
	
    map_defn := "map" "{"  map_operator*  [map_location*] [map_number] "}"
        
	map_operator :=  map_object map_object_name ["with" (map_property_defn ",")* map_property_defn] ";" 
	
	map_property_defn := (map_property | map_object_name("." map_property[swarm_number])+ ) equation_signal value
		
	map_property := cname
	
	map_object := cname
	
	map_element := [scenario_name "."] map_object_name ("." map_property [swarm_number])*
	
	tolerance :=  "tolerance" "=" "["number "," number"]" 	
	
	map_relative_elements:= map_element("," map_element)+
	
	map_location := "same" "(" map_relative_elements ")" ";"      ->same
					|  "connect" "("  map_relative_elements ["," "interval" "=" (TRUE | FALSE)] ")" ";" 	->connect

				
    map_number := "Scenario" "." "setMapNumber" "(" number ")" ";"
    	


**描述**：

`map_defn`定义静态地图描述模块中包含的内容和相应的语法结构

**说明**

1.地图的对象`map_object`和属性`map_property`是一一对应的，即道路<Road>包含道路的属性`<RoadProp>`

有些属性返回一个或多个对象，比如`Lane`中`left_boundary, right_boundary`返回`Boundary`对象，因此可以调用`<BoundaryProp>`,再比如`Junction`中`road`返回交叉口中所有的道路，可以用`junction.road[2]`来指定第几条道路

	Lane lane with lane.left_boundary.type = 
	Junction junction with junction.road[2].type = 'one_way_two_lane'

2.`map_location`表示共有的地图元素之间的地理关系.值得注意的是拼接是同类型元素的拼接，Junction和Junction拼接，road和road拼接， lane和lane拼接
>+ same:= 表示同一个地图元素
>+ connect：表示地图元素相邻, interval为True表示地图元素之间存在间隔，可以包含其他地图元素，False表示直接相邻，即第一个地图元素的出口接第二个元素的入口。默认是False。

Road and Road
 道路宽度可以不一致,比图两车道的道路road1可以和三车道的道路road2拼接，但此时需要interval为True,中间可能包含road，junction；interval=False，则中间夹一个连接的road作为连接
>+ `connect(road1,road2,iterval = True)`
>+ `connect(road1,road,road2,iterval = False)`
![image](https://github.com/ClorisUcas/A-scenario-description-language/blob/main/documents/pictures/road-road.png)

 两条相同宽度道路的直接拼接

Junction and Junction
 interval= False, 我们认为是Junction之间共享一条road，而为True则认为中间间隔一些Road，Junction..

Junction and road
 拼接的是Junction中某条道路和road

3.`<map_element>`中的`<map_object_name>`除了表示`<map_operator>`的对象名称，还表示地图元素属性中的对象列表，比如例子中交叉路口中的道路列表 `crossroad.road[2]`也是`<map_element>`

4.`Scenario.map_number(<number>)`指明筛选符合条件地图的数量，如果未定义，默认是筛选所有地图

####测试用例初始化描述####

**定义**

    init_defn :=  "init" "{"  [max_duration_time_defn] [scenario_number]  object_defn+  position+  [max_duration_time_defn] [scenario_number] "}"
    
    object_defn := object object_name  ["with" (object_property_defn ",")* object_property_defn] ";"
    
    object_property_defn := [object_name swarm_number "."] object_property equation_signal value
    
    position := object_name [swarm_number] "." (set_relative_position | set_position)
    
    rp_heading := cname
    rp_distance_unit := cname "." cname
    rp_relative:= object_name[swarm_number]
    rp_left_distance := number
    rp_front_distance := number
    rp_right_distance := number
   
    set_relative_position :=  "setRelativePosition" "(" "heading" "=" rp_heading "," "relativeto" "=" rp_relative ["," "left_distance" "=" rp_left_distance][ "," "front_distance" "=" rp_front_distance] ["," "right_distance"  "=" rp_right_distance]["," "distance_unit" "=" rp_distance_unit] ")" ";"  
    
    set_position := "setPosition""(" map_element ")"";"

    max_duration_time_defn := "Scenario"".""setDurationtime""(" ( (["min" "=" min_time ","] "max" "=" max_time) | ("min" "=" min_time [ "," "max" "=" max_time]) ) ")" ";"    
   
    scenario_number := "Scenario"".""setScenario""("( (["number" "=" number ","]  "type" "=" scenario_type) |("number" "=" number [","  "type" "=" scenario_type]))")"";"    
    
    scenario_type := SCENARIO_TYPE
    SCENARIO_TYPE:= "Success" | "Fail" | "All"
	
**描述**

场景初始化包括场景中参与者对象的初始化object_defn，测试用例场景生成的最大数量scenario_number设置，以及参与者之间初始位置的摆放position

**说明**
1.初始化举例:=一个测试用例生成的最大时间是120s，初始化陪测车辆car和被测车辆aut，分别设置相对位置和绝对位置的关系

	init{
		Scenario.setDurationtime( max = 120);
		Car car with color='blue';
		Aut aut;
		aut.setPosition(road.left_lane)
		cat.setRelativePosition(relativeto=aut, front_distance=100, right_distance=[1:=2])
	}


2.`<object>`和`<object_property>`以及`<object_behavior> `是对应的关系，即Car有汽车的属性和行为方法。

3.当两个位置关系函数不被调用时，默认放置在地图的某个起点处。其中`setPosition`： 设置绝对位置，参数position用地图元素表示；`setRelativePosition`： 设置相对位置

- heading： 朝向
- relativeto： 相对参与者
- left_distance： 与相对的对象左侧距离
- front_distance： 与相对的对象前方距离
- right_distance： 与相对的对象右侧距离
-  distance_unit： 距离单位

4.`<max_duration_time_defn> `最大时间不设置时，则采用系统内的最大时间

5.`<scenario_number>`设置具体场景生成的数量，默认是全部生成，如果指定类型是`Success`则只生成成功执行的测试用例，`Fail`则生成执行失败的测试用例

####测试用例动作执行描述####
	
**定义**

    execute_defn :=  "execute" "{" (event_defn | emitevent | composite_behaviors)* "}"
    
    event_defn := "Listen" event_name "with" composite_conditions  [ "do" (behavior_defn | composite_behaviors)] ";"
  
    
    composite_behaviors :=  temporal  "{" ([behavior | emitevent | composite_behaviors])*  "}"
    
    emitevent := (left_braket emitevent right_braket) | (emitevent join_operator emitevent) | emitevent_defn
    
    behavior := [pre_constraits] behavior_defn [post_constraits] ";"
    
    pre_constraits:= "[" composite_constraints  "]"
    
    post_constraits:= "[" composite_constraints "]"
    
    temporal:=  [pre_constraits]    ( (temporal_operator_sp "(" [interval_defn]")")  
    | (repeat "(" (([ "execution_times" "=" execute_times ["," "duration" "=" interval_defn]]) 
    | (["duration" "=" interval_defn ["," "execution_times" "=" execute_times]])) ")" ) ) [post_constraits]
    
    repeat:= REPEAT
    temporal_operator_sp := SERIAL | PARALLEL
    SERIAL := "serial"
    PARALLEL:= "parallel"
    REPEAT:= "repeat"
   
    execute_times := number
    
    constraints := emitevent_defn | condition_defn
    
    composite_constraints := (left_braket composite_constraints right_braket) | (composite_constraints join_operator composite_constraints) | constraints
    
    emitevent_defn := "@" event_name "(" [execute_times] ")" 
    
    composite_conditions:=  (left_braket composite_conditions right_braket) | (composite_conditions join_operator composite_conditions) | condition_defn
    
    
    condition_defn :=  [not ] behavior_defn [judge_signal  c_value]
    
    behavior_defn := object_name [swarm_number] ("." object_behavior)+
    
    param := cname ["=" value]
    
    object_behavior:= cname ["("[param ("," param)*]")"]

**描述**

测试用例的执行过程，对象之间动作的交互，设计利用正则表达式设计关系。 CA:=:==AA | CA;CA（serial） | CA||CA（parallel） | CA*(repeat) | <condition> CA | CA [condition]

**说明**

1.`<execute_defn>`案例

案例1:= 被测车辆启动时，车流加速直行直到目标速度为20km/h，当车流中第一辆成距离被测车辆60m时，开始加速到50km/h,第二辆车在第一辆车加速完成后，开始右转。

	execute{
		serial(){
		    [aut.isStart()] carFlow.goStraight(type = 'accelerate', targetSpeed=20);
		    [carFlow[0].distance(aut) <= 60 ] carFlow[0].goStraight(type = 'accelerate', targetSpeed=50);
		    carFlow[1].turnRight();
		}
	}

案例2:=被测车辆在1到10s内加速直行

	serial([1:=10]){
	    car1.goStraight(type = 'accelerate', targetSpeed=20);
	}
	

案例3:=在一个双向双车道上，被测车辆遇到前车急刹车同时对侧车道有车驶来时的处理能力
    map{
        Road r with type = 'two_way_two_lane';
    }

	init{
           AUT_Car aut;
           Car car1;
           Car car2;
           aut.setPosition(r.right_lane);
           car1.setRelativePosition(heading = aut.same, relativeto =aut,  front_distance = 30) ;
           car2.setRelativePosition(heading = aut.opposite, relativeto = aut,  front_distance = 100, left_distance = 20) ;
	}
	
	execute{
		parallel(duration=[:=30]){
			car1.brake() ;
			car2.goStraight(type = 'accelerate');
		}
	}

案例4：被测车在隧道内遇到前车蛇形前进

	repeat(time = 4 ,duration=[:=30]){
		[aut.isStart()] car1.changeLane(path = r.left_lane, duration = 5);
        car1.changeLane(path = r.right_lane, duration = 5);  
	}

案例5：车辆开始右转直到车辆距离行人小于30m时，车辆刹车，与此同时，4s后行人开始过马路。当与行人距离大于30m时，车辆直行。
	
	parallel(){
		serial(){
			Scenario.wait(4);
			pedestrainflow.cross();
		}
		serial(){
			car.turnRight() [car.distance(pedestrainflow) <=30];
			car.brake();
		}
	}
	[car.distance(pedestrainflow) >30 ] car.goStraight();
	
案例6：当被测车辆速度达到90km/h,对侧车辆开始加速，且行人开始过马路
	
	<aut.getSpeed == 90> parallel(){
		car.goStraight(type='accelerate');
		pedestrainflow.cross();
	}


2.时序逻辑算子介绍

>+ `serial`表示第一个动作结束后，第二个动作开始执行，在案例1中`serial()`没有参数`duration`表示该`serial`模块没有时间的限制,案例2表示`serial`场景生成在1到10s内，这里省略了`duration`
>+ `parallel`表示两个事件同时开始,可以同时结束也可以不同时结束，默认是按照执行时间最长的动作为场景的结束。案例3表示`parallel`场景生成时间在30s内。但是在`parallel`执行的动作中可以设置持续时间，或者作为条件的执行时间，因此可以表示非同时开始或者非同时结束的事件，比如案例5。
>+ `repeat`表示重复，`time`表示重复的次数，`duration`表示持续的时间，这里`time`和`duration`不可以省略，除非两者都调用，且顺序是`time，duration`，比如案例4可以写为`repeat(4,[:=30])`,但不能写为`repeat([:=30],4)`。

时序逻辑算子可以相互嵌套使用，比如案例5。


3.在行为`<behavior_defn>`前和时序逻辑算子`serial,parallel,repeat`前的`[]`表示条件，当满足条件`<Constraints>`时行为开始，在后的`[]`表示终止条件，当满足条件`<Constraints>`时行为终止。案例6描述了在`parallel`前的前置条件。
 
4.`<event_defn>`定义一个事件，监听事件`<composite_conditions>`或者一个行为`<behavior_defn>`，事件起名为`<event_name>`, ()中的number表示监听次数，默认是一次，do表示监听到事件后相应的行为。其中案例4表示`do`后面可以接`composite_behaviors`

案例1：监听被测车辆的速度值，当速度大于50km/h,陪测车辆直行
	
	Listen aut_speed with aut.getSpeed()>50 do car.goStraight() ;

案例2：监听被测车辆左转的行为是否开始

	Listen aut_turnleft with aut.turnLeft() ;

案例3：监听被测车辆左转的行为是否完成

	Listen aut_isleft with aut.isTurnLeft() ;

案例4

	Listen aut_speed with aut.getSpeed()>50 do serial(){car.goStraight()} ;


5.使用`@`调用监听的事件，可以放在时序逻辑词的前一行，表示监听整个序列，如案例1；也可以放在行为前`<behavior_defn>`表示监听该行为之后的所有行为；亦或者置于行为`<behavior_defn>`的条件当中，只在当前行为中监听。事件监听的顺序是@的顺序。`<number>`表示监听的次数，默认是1次

案例1

	@aut_turnleft()
	serial(){...}

案例2

	serial(){
		@aut_turnleft() && aut_speed()	
		behavior1;
		behavior2;
	}
	
案例3
	
	<@aut_isleft() || aut_speed()> behavior;
	behavior [@aut_isleft() || aut_speed()]


6.每一个`<object_behavior>`都有四个状态`start，end，run, fail`状态，可以利用`<object_name>.<object_behavior>.status`获取当前动作执行的状态


####测试用例测试标准描述####

**定义**

    oracle_defn :=   "oracle" "{" oracle_operator_defn* "}"
    oracle_operator_defn:= oracle_operator ":=" oracles_defn ";"   	
    oracle_operator:= ORACLE_OPERATOR
    ORACLE_OPERATOR := "periodic" | "final" | "record"
    oracles_defn:=  (left_braket oracles_defn right_braket) | (oracles_defn join_operator oracles_defn) | condition_defn
 
					  

**描述**

测试用例的评测标准，包括数据的记录和测试标准

**说明**

1.`<oracle_operator_defn>`中

>+ periodic表示在场景生成过程中的定期检查 所有的行为定义是True就是成功的行为
>+ final表示场景生成后最后的检查
>+ record表示记录一些数据

可以任意选择`periodic，final，record`中的任意一个，并可以重复多次，但是重复表示`&&`关系，比如

	perodic:= !aut.isCollided();
	perodic:= !Scenario.isTimeout();

等价于

	perodic:= !aut.isCollided() && !Scenario.isTimeout();


2.案例：在场景生成中被测对象`aut`不能发生碰撞和超时，在场景生成后测试`au`t是否完成了左转，在整个场景中记录被测对象闯红灯的状态和速度的变化。

	oracle{
		perodic:= !aut.isCollided() && !Scenario.isTimeout();
		final:= aut.isTurnLft();
		record:= aut.isRunRedLight() ||  aut.getSpeed();
	}
###测试套件###

**定义**

    suite_defn := "suite" suite_name "(" ")" "{"  [composite_scenarios]  "}"
    
    composite_scenarios:= (map_location | object_mix_defn | transition_params)*   sp_mix_operator*
    
    sp_mix_operator := (temporal_operator_sp | connect) "("  [interval_defn] ")" "{" (sp_mix_operator | (scenario_name ";") )* "}"
    
    connect:= CONNECT
    CONNECT:= "connect"
    
    suite_object_defn:=  scenario_name "." object_name
    
    object_mix_defn := "Suite" "." "setSameObject" "(" suite_object_defn [(","  suite_object_defn)*] ")" ";"
    
    transition_params := "Suite" "." "setSuiteParams" "("  [(["transition_scenario"  "=" boolean ","]  "transition_time" "=" interval_defn) | (["transition_time"  "=" interval_defn","]  "transition_scenario" "=" boolean )]   ")" ";"


**描述**

测试套件表示多个测试用例（测试场景）的组合场景，组合的方式有两种，一种是直接生成测试用例的场景不考虑场景内部组合，第二种是组合测试用例的场景生成新的测试场景。

**说明**

1.`<sp_mix_operator>`表示第一种情况，测试用例之间不存在组合关系，只是场景生成先后顺序不同.其中支持`serial`和`parallel`的嵌套，比如例子2

案例1:= `scenario1`生成后间隔3s，再生成`scenario2`。

		testsuite mysuite{
			serial(){
				scenario1;
				scenario2;
			}	
		}

案例2： `scenario1`生成后,同时生成`scenario2，scenario3`。

	testsuite mysuite{
			serial(){
				scenario1;
				parallel(){
					scenario2;	
					scenario3;
				}
			}	
		}

2.`<connect>`表示第二种情况，空间和时间上重组一个新的场景。表示上只是在`<sp_mix_operator>`添加了一层`connect(){}`。场景的重组将从两个大的角度考虑，一个是空间上的重组，一个是时间上的重组。空间上的重组和地图场景描述类似，不同的是`<map_element>`新增路径的表达。如果没有显示定义`<map_object_location>`则随机选择一种可行的地图重组生成场景

		<map_element> :=:== <scenario_name>.<map_object_name> |<scenario_name>.<map_object_name>.<MapProperty>'['<number>']'

时间上的重组需要考虑几种情况：

- 同一个npc： `<object_mix_defn>`考虑多个场景中描述的被测对象或者陪测对象是同一个。
- 在运行时保证速度和地图位置的连续性：分两种情况。一种是使用过渡场景`transition_speed= True`，用于速度的过渡，这个时候不同测试用例拼接时可以有不同的初始点，但是需要检查地图和过渡地图拼接的合理性；另一种是不考虑速度过渡`transition_speed=  False`，直接拼接场景，此时需要检查速度在初始的变化是否会影响用例的执行。`transition_speed`默认是True, `transition_time`表示过渡场景的时间约束。
- 每一个suite会自动创建Suite类。

![b.png]( https:=//i.loli.net/2021/05/28/TXYLinGkgV47sZK.png)

案例1：考虑没有过渡场景的场景重组。`scenario1`中的`road`和`scenario2`中的`road`拼接，指定两个测试用例中的被测车辆是同一辆车，顺序执行两个测试用例场景。

	testsuite mysuite{
		touch(scenario1.road,scenario2.road);
		Suite.setSuiteParams(transition_scenario = False) ;
		Suite.setSameObject(scenario1.aut , scenario2.aut);
		connect()
			serial(){
				scenario1;
				scenario2;	
			}
		}	
		}


案例2：考虑过渡场景的场景重组。`scenario1`中的`road`和`scenario2`中的`road`中存在过渡道路，指定两个测试用例中的被测车辆是同一辆车，顺序执行两个测试用例场景。

	testsuite mysuite{
		interval(scenario1.road,scenario2.road);
		Suite.setSuiteParams(transition_scenario = True, transition_time = [:=10]) ;
		Suite.setSameObject(scenario1.aut , scenario2.aut);
		connect()
			serial(){
				scenario1;
				scenario2;	
			}
		}	
		}
	
##配置文件使用##


将一些scenario作为系统内置的场景，给出配置文件,在main中调用`sys.<scenario_name>`掉用即可。

案例：配置文件格式
	params = {'road_number' := 4,
			  'color':= red }
	"run" "sys""."scenario_name "with" params

