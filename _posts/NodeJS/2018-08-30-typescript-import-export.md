---
layout: post
categories: NodeJS
tags: [ es6, typescript, commonjs, babel, import, export ]
title: "Typescript 에서 import 와 export 를 어떻게 처리할까?"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
date: 2018-08-30
---

ES6 에서 모듈을 불러오고 내보낼 수 있는 **import** 와 **export** 키워드가 새로 추가 되었다. 기존에는 환경에 따라 AMD, CommonJS, UMD 로 나누고 저 마다 각 모듈 시스템을 가지고 사용하고 있었다. 현재 **CommonJS** 기반의 NodeJS 에서는 부분적으로 ES6 사용이 가능한 것 같고 완벽한 ES6 또는 NEXT(최신버전) 를 사용하기 위해서는 **Babel** 이나 **Typescript** 의 도움을 받아야만 한다. 이번 포스팅은 **Typescript** 를 통해 **import** 와 **export** 를 사용했을 때 어떻게 transpile 되는지 알아 보려고 한다. 또 **Babel** 과도 어떤 차이가 있는지 비교해 볼 것이다. transpile target 은 가장 안정적으로 돌아가는 ES5 로 하겠다.

<br>

# 1. CommonJS 와 ES6 비교

### 1. default 로 내보내고 불러오기

- sum.js

```js
// CommonJS
module.exports = function(arg1, arg2) { return arg1 + arg2; };

// ES6
const sum = (arg1, arg2) => arg1 + arg2;
export default sum;

// ES6
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

### 2. property 로 내보내고 불러오기

- sum.js

```js
// CommonJS
exports.sum = function(arg1, arg2) { return arg1 + arg2; }; // exports = module.exports

// ES6
const sum = (arg1, arg2) => arg1 + arg2;
export { sum };

// ES6
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

// ES6
import { sum } from './sum';
sum(1, 2);
```

<br>

# 2. Typescript 에서 import 와 export 의 transpile

**Typescript** 에서 **CommonJS** 기반의 모듈을 어떻게 **import** 하는지 살펴보기 위해 '**3rd-module.js**' 를 추가했다. 아직까지는 <u>npm 에 올라와 있는 대부분의 모듈들이 <b>CommonJS</b> 기반으로 제공되고 있다는 점을 유의해야 한다.</u>

### 1. ES6(Typescript)

- ts-module.ts

```js
const tsModule = 'tsModule';
const tsModuleProperty = 'tsModuleProperty';

export { tsModuleProperty };
export default tsModule;
```

- 3rd-module.js (npm install 을 통해 다운 받을 수 있는 **CommonJS** 기반의 모듈이라고 생각)

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

### 2. ES5(transpiled)

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

- console

```console
tsModule
tsModuleProperty
undefined // default property 값을 가져오지 못함
thirdModuleProperty
```

<hr>

**module.exports** 와 **export default** 가 같다고 생각해 왔던 사람들에게는 세번째 값이 **undefined** 가 나온 것에 대해 의아해 할 수 있다. 기존에 사용하던 **CommonJS** 환경과 비슷하게 보였던 것은 여태껏 **Typesciprt** 가 transpile 되면서 **import** 와 **export** 를 사용하면 알아서 **default** 프로퍼티를 처리해 주었기 때문이다.

```js
// before transpile
export { tsModuleProperty };
export default tsModule;

// transpile
exports.tsModuleProperty = tsModuleProperty;
exports.default = tsModule;
```

**exports** 변수에 프로퍼티를 추가하는 형태로 transpile 되었다. <u><b>export default</b> 가 <b>module.exports</b> 로 transpile 되지 않았다는 것</u>에 유의해야 한다.

```js
// before transpile
console.log(tsModule);
console.log(tsModuleProperty);

console.log(thirdModule);
console.log(thirdModuleProperty);

// transpile
console.log(ts_module_1.default);
console.log(ts_module_1.tsModuleProperty);

console.log(_3rd_module_1.default);
console.log(_3rd_module_1.thirdModuleProperty);
```

**import** 를 통해 모듈을 불러와 console 에서 사용할 때에도 **default 프로퍼티**를 추가하고 있다. 아마도 ES6 에서는 모듈을 내보낼 때 <u>default 형태</u>와 <u>property 형태</u>로 동시에 내보낼 수 있기 때문에 이렇게 처리하지 않았을까 생각해 본다. 따라서 console 에서 undefined 값이 나온 이유는 **CommonJS** 기반의 모듈이 **default 프로퍼티**가 없는 상태로 export 되었기 때문이다.

<u>앞으로 <b>import</b> 를 <b>default 프로퍼티</b>가 있는 모듈을 불러오는 키워드라고 생각하면 되겠다.</u>

### 3. CommonJS 기반의 모듈을 import 할 때에는 어떻게 해야 될까?

```js
// before transpile
import * as thirdModule from './3rd-module';
console.log(thirdModule);

// transpile
var thirdModule = require("./3rd-module");
console.log(thirdModule);

// console : { thirdModuleProperty: 'thirdModuleProperty' }
```

모듈의 프로퍼티를 사용하려고 평소처럼 위와 같이 불러 온다면 모듈 자체가 **import** 될 수 있다. 다시 말하면 transpile 되면서 **default 프로퍼티**를 추가하지 않기 때문에 기존의 **CommonJS** 방식으로 모듈을 사용할 수 있게 된다는 말이다. thirdModule 이 transpile 되면서 **default 프로퍼티**를 추가하지 않았고 그 결과 console 에서는 객체정보가 잘 출력되었다.

### 4. export default 된 모듈을 require 할 때에는 어떻게 해야 될까?

```js
const thirdModule = require('./3rd-module').default;
```

**export default** 는 **exports.default** 로 transpile 될 것이다. **import** 를 사용해 모듈을 불러 온다면 **transpile** 되면서 **default 프로퍼티**를 자동으로 추가해 주겠지만 **require** 를 사용할 경우 개발자가 직접 **default 프로퍼티**를 추가해 코딩해 주면 된다.

<br>

# 3. Babel VS Typescript

**Babel** 에서 **CommonJS** 기반의 모듈을 어떻게 **import** 하는지 살펴보기 위해 '**commonjs-module.js**' 를 추가했다.

### 1. ES6

- es6-module.js

```js
const es6Module = 'es6Module';
const es6ModuleProperty = 'es6ModuleProperty';

export { es6ModuleProperty };
export default es6Module;
```

- commonjs-module.js

```js
const commonjsModule = {
  commonjsModuleProperty: 'commonjsModuleProperty'
};

module.exports = commonjsModule;
```

- index.js

```js
import es6Module, { es6ModuleProperty } from './es6-module';
import commonjsModule, { commonjsModuleProperty } from './commonjs-module';

// es6-module.js
console.log(es6Module);
console.log(es6ModuleProperty);

// commonjs-module.js
console.log(commonjsModule);
console.log(commonjsModuleProperty);
```

### 2. ES5(transpiled) / presets: es2015

- es6-module.js

```js
'use strict';

Object.defineProperty(exports, "__esModule", { value: true });

var es6Module = 'es6Module';
var es6ModuleProperty = 'es6ModuleProperty';

exports.es6ModuleProperty = es6ModuleProperty;
exports.default = es6Module;
```

- commonjs-module.js

```js
'use strict';

var commonjsModule = {
  commonjsModuleProperty: 'commonjsModuleProperty'
};

module.exports = commonjsModule;
```

- index.js

```js
'use strict';

var _es6Module = require('./es6-module');
var _commonjsModule = require('./commonjs-module');

var _es6Module2 = _interopRequireDefault(_es6Module);
var _commonjsModule2 = _interopRequireDefault(_commonjsModule);

function _interopRequireDefault(obj) {
  return obj && obj.__esModule ? obj : { default: obj };
}

// es6-module.js
console.log(_es6Module2.default);
console.log(_es6Module.es6ModuleProperty);

// commonjs-module.js
console.log(_commonjsModule2.default);
console.log(_commonjsModule.commonjsModuleProperty);
```

- console

```console
es6Module
es6ModuleProperty
{ commonjsModuleProperty: 'commonjsModuleProperty' }
commonjsModuleProperty
```

<hr>

위에 있는 **Babel** 예제는 파일명과 변수명만 바뀌었을 뿐 **Typescript** 예제와 동일하다. 하지만 신기하게도 **Babel** 의 transpile 은 세번째 값이 재대로 출력되었다. **Typescript** 에서 **default 프로퍼티**를 처리하는 방식과 **Babel** 에서 **default 프로퍼티**를 처리하는 방식이 특별히 다르지 않은 것 같은데 어떤 이유로 이런 결과가 나올까?

```js
// es6-module.js
Object.defineProperty(exports, "__esModule", { value: true });

// commonjs-module.js
// 'use strict' 가 추가되었을 뿐 특별한 traslate 가 없다.

// index.js
function _interopRequireDefault(obj) {
  return obj && obj.__esModule ? obj : { default: obj };
}
```

**Babel** 로 transpile 된 js 들을 살펴보면 **Typescript** 의 transpile 에서는 없는 특별한 부분이 있다. ES6 기반의 모듈은 **__esModule 프로퍼티**를 추가하고 내보내졌지만 **CommonJS** 기반의 모듈은 그렇지 않다. 두 모듈을 불러온 index.js 에서는 이 프로퍼티 값으로 각 모듈을 구분하고 **default 프로퍼티** 추가하는 작업을 하고 있다. 결과적으로 **CommonJS** 기반의 모듈은 **default 프로퍼티 값**으로 귀속된 새로운 객체를 반환한다. 따라서 **CommonJS** 기반의 모듈을 기존 방식으로 **import** 해도 **default 프로퍼티** 접근이 가능해져서 정상적으로 출력되는 것이다.

### 3. Babel VS Typescript

NodeJS 에서 채택한 **CommonJS** 모듈 시스템에 익숙해져서 **Babel** 의 transpile 스펙이 맞는 것 같지만 이는 **Babel** 에서 편의상 제공하는 스펙이고 **Typescript** 의 transpile 이 근본적인 스펙인 것 같다. **Typescript** 와 **Babel** 은 태생이 다르기 때문에 비교하는 것은 맞지 않고 그냥 개발환경에 따라 왜 이렇게 사용하는지 어떤 차이가 있는지 이해하면서 사용하는 것이 좋겠다.

### 4. ES6 & ES NEXT 환경에서 CommonJS 기반의 모듈을 import 하는 방법

- Typescript

```js
import * as alias from module;
```

- Babel

```js
import name from module;  // import * as alias from module; 도 가능
```

<br><br>

**※ Reference**
  * [타입스크립트 밋업 2018 1분기 첫번째 세션 손찬욱님 (NAVER) - TypeScript로부터 얻은 것과 잃은 것](https://www.youtube.com/watch?v=3M92klkicVc&list=PLV6pYUAZ-ZoG0WgaxL9sk2hDMSmXwC_HG)