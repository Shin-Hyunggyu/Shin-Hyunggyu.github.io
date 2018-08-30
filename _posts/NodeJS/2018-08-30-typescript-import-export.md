---
layout: post
categories: NodeJS
tags: [ typescript, es6, commonjs ]
title: "Typescript 에서 import 와 export 를 어떻게 처리할까?"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
date: 2018-08-30
---

ES6 에서 모듈을 불러오고 내보낼 수 있는 import 와 export 키워드가 새로 추가 되었다. 기존에는 환경에 따라 AMD, CommonJS, UMD 로 나누고 저 마다 각 모듈 시스템을 가지고 사용하고 있었다. 현재 CommonJS 기반의 NodeJS 에서는 부분적으로 ES6 사용이 가능한 것 같고 완벽한 ES6 또는 NEXT(최신버전) 를 사용하기 위해서는 Babel 이나 Typescript 의 도움을 받아야만 한다. 이번 포스팅은 Typescript 를 통해 import 와 export 를 사용했을 때 어떻게 translate 되는지 알아보려고 한다. 또 Babel 과 차이가 있는지 비교할 것이다. translate 는 가장 안정적으로 돌아가는 ES5 를 target 으로 한다.

# CommonJS 와 ES6 비교

### 1. default 로 내보내고 사용하기

- sum.js

```js
// CommonJS
module.exports = function(arg1, arg2) { return arg1 + arg2; };

// ES6
const sum = (arg1, arg2) => arg1 + arg2;
export default sum;

// 또는

export default (arg1, arg2) => arg1 + arg2;
```

- index.js

```js
// CommonJS
var sum = require('./sum');
sum(1, 2);

// ES6
import sum from './sum';
sum(1, 2);
```

### 2. property 로 내보내고 사용하기

- sum.js

```js
// CommonJS
exports.sum = function(arg1, arg2) { return arg1 + arg2; }; // exports = module.exports

// ES6
const sum = (arg1, arg2) => arg1 + arg2;
export { sum };

// 또는

export const sum = (arg1, arg2) => arg1 + arg2;
```

- index.js

```js
// CommonJS
var m = require('./sum');
m.sum(1, 2);

// ES6
import * as m from './sum';
m.sum(1, 2);

// 또는

import { sum } from './sum';
sum(1, 2);
```

# Typescript 에서 import 와 export 의 Translate

Typescript 에서 CommonJS 기반의 모듈들은 어떻게 import 해야 할 지를 살펴보기 위해 '3rd-module' 도 추가해서 살펴 보았다. 대부분의 모듈들은 CommonJS 기반으로 제공하고 있다는 점을 유의해야 한다.

### 1. Typescript(ES6)

- ts-module.ts

```js
const tsModule = 'tsModule';
const tsModuleProperty = 'tsModuleProperty';

export { tsModuleProperty };
export default tsModule;
```

- 3rd-module.js (npm install 을 통해 다운 받을 수 있는 CommonJS 기반의 모듈)

```js
const thirdModule = {
  thirdModuleProperty: 'thirdModuleProperty'
};

module.exports = thirdModule;
```

- index.ts

```js
import tsModule, { tsModuleProperty } from './ts-module';
import thirdModule, { thirdModuleProperty } from './3rd-module';

// ts-module.ts
console.log(tsModule);
console.log(tsModuleProperty);

// third-module.js
console.log(thirdModule);
console.log(thirdModuleProperty);
```

### 2. Translate(ES5)

- ts-module.js

```js
"use strict";

Object.defineProperty(exports, "__esModule", { value: true });

var tsModule = 'tsModule';
var tsModuleProperty = 'tsModuleProperty';

exports.tsModuleProperty = tsModuleProperty;
exports.default = tsModule;
```

- index.js

```js
"use strict";

Object.defineProperty(exports, "__esModule", { value: true });

var ts_module_1 = require("./ts-module");
var _3rd_module_1 = require("./3rd-module");

// ts-module.ts
console.log(ts_module_1.default);
console.log(ts_module_1.tsModuleProperty);

// third-module.js
console.log(_3rd_module_1.default);
console.log(_3rd_module_1.thirdModuleProperty);
```

- node index.js console 결과

```console
tsModule
tsModuleProperty
undefined // default property 값을 가져오지 못함
thirdModuleProperty
```

module.exports 와 export default 가 같다고 생각해 왔던 사람들에게는 세번째 값이 undefined 가 나온 것에 대해 의아해 할 수 있다. 여태껏 Typesciprt 가 import 와 export 에 대해 자동으로 default 를 처리함으로써 기존에 사용했던 CommonJS 패턴과 같았기 때문이다.

```js
// before translate
export { tsModuleProperty };
export default tsModule;

// translate
exports.tsModuleProperty = tsModuleProperty;
exports.default = tsModule;
```

Typescript 가 'ts-module' 모듈에 프로퍼티를 적절하게 추가해 주어 translate 하고 있다. 여기서 export default 가 module.exports 가 아닌 exports 에 default 프로퍼티를 추가했다는 점에 유의해야 한다.

```js
// before translate
console.log(tsModule);
console.log(tsModuleProperty);

console.log(thirdModule);
console.log(thirdModuleProperty);

// translate
console.log(ts_module_1.default);
console.log(ts_module_1.tsModuleProperty);

console.log(_3rd_module_1.default);
console.log(_3rd_module_1.thirdModuleProperty);
```

import 를 통해 모듈을 불러와 사용할 때에도 Typescript 가 자연스럽게 default 프로퍼티를 추가해 주고 있다. 아마도 ES6 에서는 모듈을 내보낼 때 default 와 property 로 동시에 내보낼 수 있기 때문에 이렇게 처리하지 않았을까 생각해 본다. 따라서 console 에 출력된 세번째 값이 undefined 가 나온 이유는 단순히 default 프로퍼티가 없었기 때문이었다.  

앞으로 import 를 사용할 때 default 를 기반한 모듈을 불러오는 키워드라고 생각하고 사용하면 되겠다.

### 3. CommonJS 기반의 모듈을 import 할 때에는 어떻게 해야 될까?

> import * as thirdModule from './3rd-module';

```js
// before translate
import * as thirdModule from './3rd-module';
console.log(thirdModule);

// translate
var thirdModule = require("./3rd-module");
console.log(thirdModule);

/*
console :
{ thirdModuleProperty: 'thirdModuleProperty' }
*/
```

property 로 내보낸 모듈을 불러오는 방식으로 선언할 경우 모듈 자체가 import 된다. 다시 말하면 Typescript 가 default 프로퍼티를 추가하지 않고 translate 하여 CommonJS 에서 사용하던 방식 그대로 모듈을 사용할 수 있게 된다는 말이다. 위에 결과를 보면 알 수 있듯이 thirdModule 을 translate 하는데 있어 default 프로퍼티가 추가되지 않았고 console 에는 객체가 그대로 출력되었다.

### 4. export default 된 모듈을 require 할 때에는 어떻게 사용해야 할까?

> const thirdModule = require('./3rd-module').default;

export default 는 Typescript 에 의해 exports.default 로 translate 된다. import 를 사용하면 자동으로 default 프로퍼티를 추가해 처리해 주지만 require 를 사용할 경우 개발자가 직접 default 프로퍼티를 붙여주어야 한다.