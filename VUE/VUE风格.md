# VUE风格


1. 组件名应为多个单词避免与HTML冲突
2. Prop 应尽量详细至少包含数据类型
3. 只应该拥有单个活跃实例的组件应该以 The 前缀命名, 以示其唯一性( TheHeader, TheFooter )
4. 和父组件紧密耦合的子组件应该以父组件名称作为前缀
5. 在DOM模板中没有内容得组件应该是闭合得 (<MyComponent/>)