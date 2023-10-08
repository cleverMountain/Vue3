## ref
1. ref是一个函数，传入一个值返回一个响应式的对象
```js
function ref(value) {
  return createRef(value, false);
}
function createRef(rawValue, shallow) {
  // 已经是响应式对象则返回该响应式对象
  if (isRef(rawValue)) {
    return rawValue;
  }
  // 响应式对象
  let res = new RefImpl(rawValue, shallow);
  return res
}
class RefImpl {
  constructor(value, __v_isShallow) {
    this.__v_isShallow = __v_isShallow;
    this.dep = void 0;
    this.__v_isRef = true;
    this._rawValue = __v_isShallow ? value : toRaw(value);
    // 使用_value代理value
    this._value = __v_isShallow ? value : toReactive(value);
  }
  // .value时调用get返回_vakue
  get value() {
    trackRefValue(this);
    return this._value;
  }
  // .value = ?时
  set value(newVal) {
    const useDirectValue = this.__v_isShallow || isShallow(newVal) || isReadonly(newVal);
    newVal = useDirectValue ? newVal : toRaw(newVal);
    if (hasChanged(newVal, this._rawValue)) {
      this._rawValue = newVal;
      this._value = useDirectValue ? newVal : toReactive(newVal);
      triggerRefValue(this, newVal);
    }
  }
}
```
