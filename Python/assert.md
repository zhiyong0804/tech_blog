# assert

#### 断言
* 不带括号
* assert 时，一定不要加上括号，否则无论表达式对与错，assert 检查永远不会 fail。另外，程序中的 assert 语句，可以通过-O等选项被全局 disable

```python
def apply_discount(price, discount):
    update_price = price * (1- discount)
    assert  0 <= update_price <= price, 'price should be greater or equal to  0'
    return update_price

print('ret {}'.format(apply_discount(100, 0.2)))
apply_discount(100, 2)
```

#### 