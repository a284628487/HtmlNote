# lodash 起初是 underscore 的一个分支，后面独立出来的；

### 在 node 中使用 lodash

```
var _ = require("lodash");
var p1 = {
	name:'ccf',
	age:25
};
var p2 = {
	name:'ccf2',
	age:25
};
var p3 = {
	name:'ccf2',
	age:27
};
var ps = [p1, p2, p3];
```

- find / findIndex

```
var ur = _.find([p1, p2], {
}); // 只取第一个元素

var rs = _.findIndex([p1, p2], {
	name: "ccf2"
}); // 返回索引
console.log(rs);
```

- filter

```
var rs = _.filter([p1, p2], {
	age:25
});

var rs = _.filter([p1, p2], function (user) {
	return user.name === 'ccf2';
});
```

- pluck

```
// 抽取数组里面的元素的某个属性组成一个新的数组
var rs = _.pluck([p1, p2], 'name');
```

- map

```
var rs = _.map([p1, p2], function (user) {
	// body...
	return {
		name: user.name,
		age: user.age + 2,
		city: 'xian'
	};
})
```

- remove

```
var rs = _.remove(ps, {
	name: 'ccf2'
}); // 返回结果：被移除的结果，移除之后，ps将改变

var rs = _.remove(ps, function(user) {
	return user.name === 'ccf'
}); // 返回结果：被移除的结果，移除之后，ps将改变
console.log(rs);
```

- uniq

```
// 去重
var rs = _.uniq(ps, 'name');
console.log(rs);
```

- 链式调用

```
var chain = _.chain(ps);
var result = chain.filter({name: 'ccf2'}).remove({age: 27}).value();
console.log(result);
```

## [docs](http://lodashjs.com/docs/)

