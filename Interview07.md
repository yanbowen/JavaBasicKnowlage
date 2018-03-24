# 面试题目整理 07
<hr>   
    
![](https://i.imgur.com/gJ5xWRN.jpg)   
   
	public class Main22 {

		public static void main(String[] args) {
			Scanner scanner = new Scanner(System.in);
			int N = scanner.nextInt();
			int[] arr = new int[N];
			for (int i = 0; i < arr.length; i++) {
				arr[i] = scanner.nextInt();
			}
			scanner.close();
			Map<Integer, Integer> map = new HashMap<Integer, Integer>();
			for (int i = 0; i < arr.length; i++) {
				if (map.containsKey(arr[i])) {
					map.put(arr[i], map.get(arr[i]) + 1);
				} else {
					map.put(arr[i], 1);
				}
			}
			// hashmap按value值遍历
			List<Map.Entry<Integer, Integer>> list_Data = new ArrayList<Map.Entry<Integer, Integer>>(map.entrySet());
			Collections.sort(list_Data, new Comparator<Map.Entry<Integer, Integer>>() {
				public int compare(Map.Entry<Integer, Integer> o1, Map.Entry<Integer, Integer> o2) {
	
					if (o2.getValue().compareTo(o1.getValue()) > 0) {// 按value递减顺序排序
						return 1;
					} else if (o2.getValue().compareTo(o1.getValue()) == 0) {
						if (o2.getKey().compareTo(o1.getKey()) < 0)// 如果value相同输出key小的
							return 1;
						else
							return -1;
					} else {
						return -1;
					}
				}
			});
			// System.out.println(list_Data);//次输出格式为：[3=4, 5=3, 2=3, 4=1, 1=1]
			for (Map.Entry<Integer, Integer> v : list_Data) {// 按着输出格式遍历输出结果
				System.out.println(v.getKey() + " " + v.getValue());
			}
		}
	}  
  
---  
  
