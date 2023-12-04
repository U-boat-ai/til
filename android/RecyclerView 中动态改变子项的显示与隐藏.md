# RecyclerView 中动态改变子项的显示与隐藏

大致来说，分以下三步：
1. 重写 `getItemCount()` ，返回当前集合的数量；
2. 缓存要添加或删除的 item 及 position；
3. 根据业务逻辑调用 `mAdapter.notifyItemRemoved(position)` 或 `mAdapter.notifyItemInserted(position)`，并同步删除或新增集合的元素。

简化的代码示例：
```java
mAdapter = new MultiRecyclerAdapter<WOFlowTemplateBean>(mContext, mTemplateList) {
            @Override
            public int getItemCount() {
                return mTemplateList.size();
            }
        };

@Override
public void convert(ViewHolder holder, WOFlowTemplateBean bean, int position) {
    holder.setText(R.id.tv_item_name, bean.fieldName);
    RadioGroup rgContainer = holder.getView(R.id.rg_container);
    if ("is_pay_consistent".equals(bean.fieldNameEn)) {
        payConsistentBean = bean;
        feeConsistentPosition = position;
    }

    rgContainer.setOnCheckedChangeListener((radioGroup, checkId) -> {
        if (checkId == R.id.rb_no) {
            rgContainer.check(R.id.rb_no);
            if ("is_pay".equals(bean.fieldNameEn)) {
                for (int i = 0; i < mTemplateList.size(); i++) {
                    if (feeConsistentPosition != -1) {
                        mTemplateList.remove(feeConsistentPosition);
                        mAdapter.notifyItemRemoved(feeConsistentPosition);
                        break;
                    }
                }
            }
        } else if (checkId == R.id.rb_yes) {
            if ("is_pay".equals(bean.fieldNameEn) && bean.uploadValue == 0) {
                for (int i = 0; i < mTemplateList.size(); i++) {
                    if (feeConsistentPosition != -1) {
                        mTemplateList.add(feeConsistentPosition, payConsistentBean);
                        mAdapter.notifyItemInserted(feeConsistentPosition);
                        break;
                    }
                }
            }
            rgContainer.check(R.id.rb_yes);
        }
    });
}
```