#Paser process

>+ paser tool: Lark
>+ program language: Python
>+ process:

![image](https://github.com/ClorisUcas/A-scenario-description-language/blob/main/documents/pictures/parser.png)

	输入：脚本文件
	输出：json数据
	过程：1.根据定义的BNF利用Lark函数构造抽象语法树
		2.将抽象语法树转换成json数据格式的文件
		3.检查静态的语义，比如所有object及其对应的属性和方法是否是已经定义好的；对象是否被实例化
		4.如果通过检查就输出json数据，否则报出错误的原因，程序结束。

json格式

	{
	    'scenarios': [{
	        'scenario_name': ,
	        'params': {},
	        'map': {
	            'submaps': [{
	                'params': [],
	                'type': ,
	                'name': 
	            }],
	            'relations': [{
	                'type': ,
	                'element': []
	            }],
	            'map_number': 
	        },
	        'actors': {
	            'scenario_params': {},
	            'actors': [{
	                'params': [],
	                'type': ,
	                'alias':,
	                'absolute_position': 
	            }]
	        },
	        'execute': {
	            'behaviors': [{
	                'behaviors': [{
	                    'behavior_defn': 
	                }],
	                'params': {},
	                'type': 
	            }]
	        },
	        'oracles': [{
	            'type': ,
	            'condition': []
	        }]
	    }],
	    'suite': {
	        'suite_name': ''
	    },
	    'main': {
	        'map_file': ,
	        'map_relation': [],
	        'object_relation': [],
	        'transition_params': {},
	        'scenarios': [{
	            'scenarios': []
	        }],
	        'type': 
	    }
	}



