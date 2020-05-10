# 仅用递归逆序一个栈



递归调用方法时，系统会把方法隐式地存到一个“系统栈”里面，即在方法即将进入下一层（即下一次调用自身）前，把本层内部的变量存储到系统的某些位置（我们不用关心），当递归调用从最底层开始回退，回退到本层的时候，方法会自动重新加载之前存储的数据。我们可以利用这一点来隐式地存储我们逆序目标栈过程中需要存储的数据。另外需要注意的是，在JAVA中按引用传参时，形参和实参指向同一个对象，此时方法内部对形参的操作实际上是对实参的操作。

```
import java.util.Stack;

public void stackReverse(Stack<Integer> stack){
    if(stack.isEmpty())
        return
    else{
        int botton=getAndRemoveBotton(stack);
        stackReverse(stack);
        stack.push(botton);
    }
}

public int getAndRemoveBotton(Stack<Integer> stack){
    int top=stack.pop();
    if(stack.isEmpty())
        return top;
    else{
        int t=getAndRemoveBotton(stack);
        stack.push(top);
        return t;
    }
}

public static void main(String[] args){
    Stack<Integer> stack=new Stack<Integer>();
    stack.push(1);
    stack.push(2);
    stack.push(3);
    stack.push(4);
    stack.push(5);
    
    stackReverse(stack);
    
    while(!stack.isEmpty()){
        System.out.println(stack.pop());
    }
}
```

- stackReverse：依次取出栈底元素：1->2->3->4->5，存储在每一层的递归调用中（botton）。栈空后，方法开始回退，逐层执行push，（逆序地）把原栈底元素（botton）重新入栈：5->4->3->2->1。此时栈已逆序，算法完成。
- getAndRemoveBotton：取出栈底元素，使栈的高度减一，其余元素顺序不变。该方法在每一层调用时先取出栈顶元素，一直递归地往下取到栈空（利用“系统栈”来存储目标栈的元素）为止。当栈被取空，说明刚被取出的元素为栈底元素。此时开始回退递归，在每一层把pop出来的栈顶元素（top）又push回去，保持了栈的顺序不变，并把栈底元素一层层return上来（t）。如此即可在不改变栈顺序的情况下取出栈底元素。
- 算法的关键是：巧妙利用方法递归调用、回退的顺序，和栈元素出栈、入栈的顺序的对应关系来实现操作过程中数据的存储。

