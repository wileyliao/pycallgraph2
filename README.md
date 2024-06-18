# pycallgraph2
check calling relation <br>

1. pip install pycallgraph2<br>
2. download Graphviz from: https://graphviz.org/<br>
3. add Graphviz "bin" floder to system path<br>
4. type "dot -version" to check<br>



```python
from pycallgraph2 import PyCallGraph
from pycallgraph2.output import GraphvizOutput

## 定義一些示例函數
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
```

# ast + graphviz
```python
import ast
import graphviz

class FunctionCallVisitor(ast.NodeVisitor):
    def __init__(self):
        self.calls = []

    def visit_Call(self, node):
        if isinstance(node.func, ast.Name):
            self.calls.append((node.func.id, node.lineno))
        self.generic_visit(node)

class FunctionDefVisitor(ast.NodeVisitor):
    def __init__(self):
        self.functions = {}
        self.calls = []

    def visit_FunctionDef(self, node):
        self.functions[node.name] = node.lineno
        call_visitor = FunctionCallVisitor()
        call_visitor.visit(node)
        for func_call in call_visitor.calls:
            self.calls.append((node.name, func_call[0]))
        self.generic_visit(node)

def parse_file(filename):
    # 使用指定的編碼讀取文件
    with open(filename, "r", encoding="utf-8") as source:
        tree = ast.parse(source.read())
    return tree

def generate_call_graph(tree):
    function_visitor = FunctionDefVisitor()
    function_visitor.visit(tree)

    dot = graphviz.Digraph(comment="Function Call Graph")

    for function in function_visitor.functions.keys():
        dot.node(function, function)

    for caller, callee in function_visitor.calls:
        dot.edge(caller, callee)

    return dot

if __name__ == "__main__":
    filename = "python_file.py"  # 欲產生calling graph 的python檔
    tree = parse_file(filename)
    dot = generate_call_graph(tree)
    dot.render("python_file Graph", format="png", cleanup=True)
```
