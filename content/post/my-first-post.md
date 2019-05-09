---
title: "go语言实现--从两个数组中找出它们的和最接近给定值的一对或多对元素"
date: 2019-05-09T22:03:08+08:00
draft: true
---

想起以前遇到的一道面试题，闲来无事把它实现一遍  

问题描述:
有两个数组，分别从两个数组取一个元素组成一对，找出使它们的和最接近给定值sum的一对或多对元素

解决一:
借助散列表，实现空间换时间提高查找效率，下面的时间复杂度和空间复杂度都是O(N)
```go

package main

import "fmt"

//它们的和等于给定值的一对或多对元素
//符合要求的元素放到一个数组中返回
func findEqualElems(arr1, arr2 []int, sum int) []int {
	if len(arr1) < 1 || len(arr2) < 1 {
		return nil
	}
	result := make([]int, 0)
	m := make(map[int]bool, len(arr2))
	for _, v := range arr2 {
		m[v] = true
	}
	for _, v := range arr1 {
		_, ok := m[sum-v]
		if ok {
			result = append(result, v, sum-v)
		}
	}
	return result
}

//它们的和最接近给定值的一对或多对元素
//符合要求的元素放到一个数组中返回
func findClosestElems(arr1, arr2 []int, sum int) []int {
	if len(arr1) < 1 || len(arr2) < 1 {
		return nil
	}
	result := make([]int, 0)
	m := make(map[int]bool, len(arr2))

	for _, v := range arr2 {
		m[v] = true
	}
	for x := 0; ; x++ {
		tmpSum1 := sum - x
		tmpSum2 := sum + x
		for _, v := range arr1 {
			_, ok := m[tmpSum1-v]
			if ok {
				result = append(result, v, tmpSum1-v)
			}
			//x=0的时候，sum+0和sum-0的结果一样的，避免重复结果
			if x == 0 {
				continue
			}
			_, ok = m[tmpSum2-v]
			if ok {
				result = append(result, v, tmpSum2-v)
			}
		}

		if len(result) >= 2 {
			break
		}
	}
	return result
}

func main() {
	arr1 := []int{-1, 5, 2, 6, 8, 40}
	arr2 := []int{11, 7, 2, -2, 9, 1}
	sum := 18
	res := findClosestElems(arr1, arr2, sum)
	fmt.Println(res)
}


```

解法二
第二个数组先做排序，遍历第一个数组，假设元素是x，利用二分查找在第二个数组中查找接近或等于sum-x的元素  
排序时间复杂度是O(NlogN)；查找的时间复杂度是(N*logN)