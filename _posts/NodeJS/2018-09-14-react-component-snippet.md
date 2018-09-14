---
layout: post
categories: NodeJS
tags: [ react, snippet ]
title: "React Component Snippet"
# author: "Lofty87" (default site.author.name)
# author-img: /assets/img/<author>.jpg (default site.author.img)
date: 2018-09-14
---

## 1. User Snippets

![React Snippet](/assets/img/posts/NodeJS/2018-09-14/react-component-snippet.png)

<br>

## 2. React Component Snippet ('rc')

```json
"react component set": {
  "prefix": "rc",
  "body": [
    "import React, { Component } from 'react';",
    "import PropTypes from 'prop-types';",
    
    "\nconst propTypes = {};",
    
    "\nconst defaultProps = {};",
    
    "\nclass $1 extends Component {",
      "\tconstructor(props) {",
        "\t\tsuper(props);",
      "\t}",
      "\n\trender() {",
        "\t\treturn (",
          "\t\t\t<div>$1</div>",
        "\t\t);",
      "\t}",
    "}",

    "\n$1.propTypes = propTypes;",
    "$1.defaultProps = defaultProps;",

    "\nexport default $1;"
  ]
}
```