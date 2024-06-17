# pycallgraph2
check calling relation <br>

1. pip install pycallgraph2<br>
2. download Graphviz from: https://graphviz.org/<br>
3. add Graphviz "bin" floder to system path<br>
4. type "dot -version" to check<br>




from pycallgraph2 import PyCallGraph
from pycallgraph2.output import GraphvizOutput

# 定義一些示例函數
def foo():
    bar()

def bar():
    baz()

def baz():
    pass

# 使用 pycallgraph2 生成調用圖
if __name__ == '__main__':
    graphviz = GraphvizOutput()
    graphviz.output_file = 'callgraph.png'  # 指定輸出文件

    with PyCallGraph(output=graphviz):
        foo()  # 調用主函數，這將觸發調用關係圖的生成

        
