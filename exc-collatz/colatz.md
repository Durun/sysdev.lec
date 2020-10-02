# 作業記録 コラッツ予想
- gcc最適化オプションは
```
gcc -O0 # 最適化なし
gcc -O3 # 最適化あり
```

- objdump 逆アセンブル https://linuxcommand.net/objdump/
```
objdump -d file.out
```

## 作業
```
gcc -O0 collatz.c -o O0.out
gcc -O3 collatz.c -o O3.out
```

```
objdump -d o0.out > o0.asm
objdump -d o3.out > o3.asm
```
o0.asm, o3.asmを比較

## 最適化なし
```asm
0000000000001159 <collatz>:
    1159:	55                   	push   %rbp
    115a:	48 89 e5             	mov    %rsp,%rbp
    115d:	48 83 ec 10          	sub    $0x10,%rsp
    1161:	89 7d fc             	mov    %edi,-0x4(%rbp)
    1164:	83 7d fc 01          	cmpl   $0x1,-0x4(%rbp)
    1168:	75 07                	jne    1171 <collatz+0x18>
    116a:	b8 01 00 00 00       	mov    $0x1,%eax
    116f:	eb 32                	jmp    11a3 <collatz+0x4a>
    1171:	8b 45 fc             	mov    -0x4(%rbp),%eax
    1174:	83 e0 01             	and    $0x1,%eax
    1177:	85 c0                	test   %eax,%eax
    1179:	74 15                	je     1190 <collatz+0x37>
    117b:	8b 55 fc             	mov    -0x4(%rbp),%edx
    117e:	89 d0                	mov    %edx,%eax
    1180:	01 c0                	add    %eax,%eax
    1182:	01 d0                	add    %edx,%eax
    1184:	83 c0 01             	add    $0x1,%eax
    1187:	89 c7                	mov    %eax,%edi
    1189:	e8 cb ff ff ff       	callq  1159 <collatz>
    118e:	eb 13                	jmp    11a3 <collatz+0x4a>
    1190:	8b 45 fc             	mov    -0x4(%rbp),%eax
    1193:	89 c2                	mov    %eax,%edx
    1195:	c1 ea 1f             	shr    $0x1f,%edx
    1198:	01 d0                	add    %edx,%eax
    119a:	d1 f8                	sar    %eax
    119c:	89 c7                	mov    %eax,%edi
    119e:	e8 b6 ff ff ff       	callq  1159 <collatz>
    11a3:	c9                   	leaveq
    11a4:	c3                   	retq
```

## 最適化あり
```asm
00000000000011e0 <collatz>:
    11e0:	83 ff 01             	cmp    $0x1,%edi
    11e3:	74 21                	je     1206 <collatz+0x26>
    11e5:	0f 1f 00             	nopl   (%rax)
    11e8:	40 f6 c7 01          	test   $0x1,%dil
    11ec:	74 0a                	je     11f8 <collatz+0x18>
    11ee:	8d 7c 7f 01          	lea    0x1(%rdi,%rdi,2),%edi
    11f2:	40 f6 c7 01          	test   $0x1,%dil
    11f6:	75 f6                	jne    11ee <collatz+0xe>
    11f8:	89 f8                	mov    %edi,%eax
    11fa:	c1 e8 1f             	shr    $0x1f,%eax
    11fd:	01 c7                	add    %eax,%edi
    11ff:	d1 ff                	sar    %edi
    1201:	83 ff 01             	cmp    $0x1,%edi
    1204:	75 e2                	jne    11e8 <collatz+0x8>
    1206:	b8 01 00 00 00       	mov    $0x1,%eax
    120b:	c3                   	retq
    120c:	0f 1f 40 00          	nopl   0x0(%rax)
```
### 違い
- callが無い
	- 再帰でなくなっている

## 答え合わせ
- Clangだとさらに最適化されるっぽい
- [Compiler Explorer](https://godbolt.org)
