### 限制对象类型

使用接口可以很方便的限制一个属性的类型，只需让这个属性的类型为该接口即可

原理：当一个属性的类型为一个接口，任意实现了该接口的对象都可称为合法类型

```typescript
interface Itype {
    name: string,
    age: number,
    isBoy: boolean
}

const person = (object: Itype) {
    console.log(object)
}
```



