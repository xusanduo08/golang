## 走出迷宫

用二维数组表示一个6行5列的迷宫，1表示该位置是堵墙，0表示该位置可以走，现在设计算法，给出走出迷宫的方案

```
6 5
0 1 0 0 0
0 0 0 1 0
0 1 0 1 0
1 1 1 0 0
0 1 0 0 1
0 1 0 0 0
```

涉及到的几个知识点：

* 用循环创建二维slice
* 使用slice来实现队列
* 用Fscanf读取文件
* 对Point的抽象

```go
package main

import (
	"fmt"
	"os"
)

func readMaze(filename string)[][]int{
	file, err := os.Open(filename) // 打开文件
	if err != nil {
		panic(err)
	}
	var row, col int
	fmt.Fscanf(file, "%d %d", &row, &col) // 读取行列数

	maze := make([][]int, row) // 接下来继续读取具体数值
	for i := range maze{
		maze[i] = make([]int,col)
		for j := range maze[i]{
			fmt.Fscanf(file, "%d", &maze[i][j])
		}
	}
	return maze
}
var dirs  = [4]point{{-1, 0}, {0, -1}, {1, 0}, {0, 1}}
type point struct {
	i, j int
}
// p是否存在grid中（判断是否越界）
// 返回p在grid中对应位置的值
func (p point) at(grid [][]int)(int, bool){
	if p.i < 0 || p.i >= len(grid) {
		return 0, false
	}
	if p.j < 0 || p.j >= len(grid[p.i]){
		return 0, false
	}
	return grid[p.i][p.j], true
}
func (p point) add(r point) point{ // 两点相加
	return point{p.i + r.i, p.j + r.j}
}
func walk(maze [][]int, start, end point)[][]int{
	// 构建一个盛放步子的容器，所有未走过的点值为0，否则不为0
	steps := make([][]int, len(maze))
	for i := range steps{
		steps[i] = make([]int, len(maze[i]))
	}
	Q := []point{start} // 要经过的点构成的队列
	// 每个点有上、右、下、左四个方向可以走
	// 当走到这点后，就要去探索该点的上下左右四个点是否可走
	for len(Q) > 0{ // 如果这个队列空了表示走完了
		cur := Q[0]
		Q = Q[1:]
		if cur == end{
			break
		}
		for _, dir := range dirs{
			next := cur.add(dir)
			val, ok := next.at(maze)
			if !ok || val != 0{ // 如果越界了，或者不能走（val不等于0）则继续探索下一点
				continue
			}
			val, ok = next.at(steps)
 			if !ok || val != 0{ // 这个点走过了则探索下一个
				continue
			}
			if next == start{
				continue
			}
			curStep, _ := cur.at(steps);
			steps[next.i][next.j] = curStep + 1
			Q = append(Q, next) // 将下一个点放到探索队列里
		}

	}
	return steps
}


func main() {
	maze := readMaze("maze/maze.in")
	steps := walk(maze, point{0, 0}, point{len(maze)-1, len(maze[0]) - 1})
	for _, row := range steps{
		for _, col := range row{
			fmt.Printf("%3d ", col)
		}
		fmt.Println()
	}
}
```

结果输出：

```
	0   0   4   5   6 
  1   2   3   0   7 
  2   0   4   0   8 
  0   0   0  10   9 
  0   0  12  11   0 
  0   0  13  12  13 
```

