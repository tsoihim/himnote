깊이가 가장 깊은 경우부터 모두 탐색, 많은 수의 완전탐색에 쓰임

Stack이나 Recursion으로 구현한다.

이로 인해 깊이가 너무 깊어지는 경우 해를 찾는 것이 불가해지는 경우가 있음

아래는 경우의 수 Python3 예제

```Python
import copy 

def perm(idx, case): 
	if idx == selection: 
		result.append(case) 
		return 
	for num in data: 
		if not num in case: 
			tmp_case = copy.deepcopy(case)
			tmp_case.append(num) 
			perm(idx+1, tmp_case) 

def comb(idx, case, start): 
	if idx == selection: 
		result.append(case) 
		return 
	for num in range(start, len(data)+1): 
		tmp_case = copy.deepcopy(case) 
		tmp_case.append(num) 
		comb(idx+1, tmp_case, num+1) 
		
def perm_dup(idx, case): 
	if idx == selection: 
		result.append(case)
		return 
	for num in data: 
		tmp_case = copy.deepcopy(case)
		tmp_case.append(num) 
		perm_dup(idx+1, tmp_case) 

def comb_dup(idx, case, start): 
	if idx == selection: 
		result.append(case)
		return
	for num in range(start, len(data)+1): 
		tmp_case = copy.deepcopy(case) 
		tmp_case.append(num) 
		comb_dup(idx+1, tmp_case, num) 
		
if __name__=="__main__": 
	data = list(range(1,5)) 
	selection = 2 
	
	result = [] 
	perm(0, []) 
	print("<permutation>") 
	print(len(result)) 
	print(result) 
	
	result = [] 
	comb(0, [], 1) 
	print("\n<combination>") 
	print(len(result))
	print(result)
	
	result = [] 
	perm_dup(0, []) 
	print("\n<permutation with dup>") 
	print(len(result)) 
	print(result) 
	
	result = [] 
	comb_dup(0, [], 1) 
	print("\n<combination with dup>") 
	print(len(result)) 
	print(result)
```