# Java基础02  
<hr>  

## 选择排序  

	public static viod selectSort(int[] arr){
	
		for(int m=0; m<arr.length-1; m++){
			for(int n=m+1; n<arr.length;n++){
				if(arr[m]>arr[n]){
					int temp = arr[m];
					arr[m] = arr[n];
					arr[n] = temp;
				}
			}
		}
	}   
  
## 冒泡排序  

	public static viod bubbleSort(int[] arr){
	
		for(int m=0; m<arr.length-1; m++){
			for(int n=0; n<arr.length-m-1;n++){
				if(arr[n]>arr[n+1]){
					int temp = arr[n];
					arr[n] = arr[n+1];
					arr[n+1 ] = temp;
				}
			}
		}
	}   
  
排序函数
   
	Arrays.sort(arr);//从小到大排序

最快的排序方式：希尔排序  
  
## 数组查找  
折半查找：前提是该数组为有序数组
