# Multilevel Tree
Manage multilevel relations in one table and query them easily.

## Abstractions
```
abstract class MultilevelTree {
	//key
	public int Id;
	//left range
	public int Left;
	//right range
	public int Right;
	//it's redundant field actually.
	//this field can be used for generating the value of 'Left' and 'Right' (Building the tree)
	//and performance considering like 'get parent by child id'.
	public int? ParentId;
}
```
For one instance (**X**):
+ If **X.Left == X.Right-1**, it doesn't have any child.
+ For each in instances (**Y**) that satisfies **Y.Left > X.Left && Y.Right < X.Right**, then **Y** is one child of **X**; Especially if **Z1.Left == X.Left + 1 && Z1.Right == X.Right - 1**, **Z1** is the only one child of **X**.
+ For each in instances (**Z**) that satisfies **Z.Left < X.Left && Z.Right > X.Right**, **Z** is the parents of **X**; Especially if **Z2.Left == X.Left - 1 && Z2.Right == X.Right + 1**, **Z2** is the parent of **X**.

You should build the tree before using it.

## Sample
Assume there are some relations among the following instances:
```
Root (Level 0)
{
	Id = 1,
	Left = 1,
	Right = 8,
	ParentId = null
}
Child 1 (Level: 1, Position: 1, Parent: Root)
{
	Id = 2,
	Left = 2,
	Right = 5,
	ParentId = 1
}
Child 2 (Level: 1, Position: 2, Parent: Root)
{
	Id = 3,
	Left = 6,
	Right = 7,
	ParentId = 1
}
Child 3 (Level: 2, Position: 1, Parent: Child 1)
{
	Id = 4,
	Left = 3,
	Right = 4,
	ParentId = 2
}
```

And then all regular queries can be done by one query:
+ Get **all parents** of **Child 3**:
	select * from source where left < 3 and right > 4;
+ Get **next level** of **Child 3**(no results for this case):
	select * from source where left = 3+1 and right = 4-1;
+ Get **the parent** of **Child 3**:
	select * from source where left = 3-1 and right = 4+1;
+ Get **all children** of **Root**:
	select * from source where left > 1 and right < 8;

