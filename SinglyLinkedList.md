# 单链表

​                                                                                                                                                                            ————基于spreng.blog的再学习



- 关于scanf函数

  > scanf("%d", &value) == 1 表示scanf正确接受了一个变量，因此输入非数字时，scanf会停止读取。

1. scanf() 函数有返回值且类型 int 型，当发生错误时立刻返回 EOF。
2. scanf() 函数返回的值为：正确按指定格式输入变量的个数；也即能正确接收到值的变量个数。



## 单链表的创建

```c
Node *create()
{
    Node *head = NULL, *tail;
    int value;
    head = tail = NULL;
    while (scanf("%d", &value) == 1)
    {
        if (tail == NULL)
            head = tail = (Node *)malloc(sizeof(Node)); // 初始化第一个节点
        else
            tail = tail->next = (Node *)malloc(sizeof(Node));
        tail->data = value;
        tail->next = NULL;
    }
    return head;
}

```

ref(APA): Spreng.Spreng的小屋.https://spreng.top. Retrieved 2024/12/25.
   ```c

   ## **单链表的定位插入**
   
   ```c
   void insert(Node *head, int value, int pos)
   {
       Node *current = head;
       Node *prev = NULL;
       // 将current定位到pos，若pos超出，则定位到最后
       while (pos-- && current != NULL)
       {
           prev = current;
           current = current->next;
       }
       prev->next = (Node *)malloc(sizeof(Node));
       prev->next->data = value;
       prev->next->next = current;
   }
   
   
ref(APA): Spreng.Spreng的小屋.https://spreng.top. Retrieved 2024/12/25.
   ```

   ps:插入至pos后一位

   ## **单链表的逆置**

   ```c
   Node *reverse(Node *head)
   {
       Node *prev = NULL;
       Node *current = head;
       Node *next = NULL;
    
       while (current != NULL)
       {
           next = current->next;
           current->next = prev;
           prev = current;
           current = next;
       }
       return prev;
   }

   
ref(APA): Spreng.Spreng的小屋.https://spreng.top. Retrieved 2024/12/25.
   ```

   ![](https://jpdsg-3.oss-cn-beijing.aliyuncs.com/image2/20250214161538028.jpg)

   > 这里的next在循环的最开始赋值是有讲究的，避免了current为NULL时执行next = current->next的潜在BUG。



   ```c
   Node * Reverse(Node * head)         //还是定义为Node*型返回头指针比较方便
   {
   	Node *tail, *prev;
       prev = head;
       while (prev->next)
       {
   		prev = prev->next;
   	}
   	tail = prev;
   	while (head != tail)
   	{
           prev = head;
           head = head->next;
           prev->next = tail->next;
           tail->next = prev;
	}
   	return tail;//return head;
}
   //main函数中 head=Reverse(head);
   
   ```

   （之前想了巨久😭）今天结合spreng简化后的代码又画图理解了一下

   ![](https://jpdsg-3.oss-cn-beijing.aliyuncs.com/image2/20250214161543225.jpg)

   ![](https://jpdsg-3.oss-cn-beijing.aliyuncs.com/image2/20250214161545741.jpg)

   ## **单链表的排序**

   ### 冒泡排序

   ```c
   Node *boubleSort(Node *head)
   {
       Node *prev = NULL;
       Node *current = head;
       Node *next = current->next;
       int flag = 1;
    
       while (flag)
       {
           flag = 0;
           while (current->next != NULL)
           {
               if (current->data > current->next->data)
               {
                   flag = 1;
                   if (prev == NULL)
                       prev = head = current->next;
                   else
                       prev = prev->next = current->next;
                   current->next = prev->next;
                   prev->next = current;
               }
               else{
                   prev = current;
                   current = current->next;
               }
           }
           prev = NULL;
           current = head;
       }
       return head;
   }
   
   
   ref(APA): Spreng.Spreng的小屋.https://spreng.top. Retrieved 2024/12/25.
   ```

   

- 如何命名以提高代码可读性

  > 通过加注释，合理命名变量实现，文中的prev、current、next就形象地表明三个节点，尽量不要取一些无意义的名字如p（除非和数学公式相关、或者有加注释）
  >
  > 较长命名的，建议用驼峰命名法或下划线命名法，如 printLink 或 print_link