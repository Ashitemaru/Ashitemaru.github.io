---
title: 安卓前端开发杂谈
date: 2021-09-01 22:05:04
category:
    - 计算机科学
---

安卓的移动端开发，属实让我血压高了一小段时间。毕竟已经接触过类似于 React Native 那种基于 TypeScript 语言的、优秀的跨平台移动端框架，再去尝试用繁琐的 Java 语言写安卓前端自然是不适应且感到烦躁。

但是毕竟是 Java 程序设计训练课程，安卓前端开发作为其最大的特色，不得不消受。

<!-- more -->

# 关于数据绑定

如果要我说最大的不适应的地方，首先就是数据和图形界面的完全分离。在 React Native 框架之中，数据和图形界面的管理大概是这样的：

{% codeblock lang:typescript TypeScript %}
const App = (props: AppProps) => {
    // Set states & initialize
    const [bar, setBar] = useState<number>(0);

    // Some functions to update or rewrite data
    const update = () => setBar((o) => o + 1);

    // Write UI with TSX, use data freely
    return (
        <View>
            <Text> Hello world! </Text>
            <Text> {`The val of bar is ${bar}`} </Text>
            <Text> {`Property foo is ${props.foo}`} </Text>
            <Button onClick={update} />
        </View>
    );
}
{% endcodeblock %}

但是在安卓之中，图形界面完全定义在 `.xml` 文件之中，而且一定程度上其定义各个组件摆放方式的语法细节的繁杂程度不低于 HTML 。而如果需要将数据载入图形界面，则首先要根据组件的 ID 获取组件，之后才能调用各种方法设定具体属性。

{% codeblock lang:java Java %}
((TextView) view.findViewById(R.id.entity_name)).setText(data.name);
{% endcodeblock %}

这里出现的 `R` 类据说是相当厉害的。但是我第一次接触安卓的时候，这个符号给我带来的感觉就是不安，因为我再也不能像写 React Native 一样至少自认为自己掌握了程序之中的数据流。换一种说法，即使 React Native 可能做了很多背后工作，但它至少做到了让开发者在很多时候能直观引导数据流。

对于按钮等组件， React Native 框架下可以直接传入回调函数。而众所周知 Java 是纯净的面向对象语言，怎么会有 lambda 这种异端呢（新版本的 Java 已经离经叛道了！）。

所以你需要写监听器对象，重写其中的方法等等。

---

并不是说这样不好，我还记得之前大一小学期的时候写 Qt 很多时候就是各种指针到处乱飞然后到处乱 `set` 这个那个的，最后一个 `draw` 函数里面上百行的，成块的各种格式设定、字体设定、内容设定、排列设定，然后这次写安卓让我回忆起了那一段黑暗时光。

# 并不适应的适配器模式

如果只是想给一个 `TextView` 载入文字，直接几个 `set` 函数就结束了。但是给一个格式复杂的列表载入数据，则相当繁琐。比如说现在主流的 `RecyclerView` ，其载入数据，就需要你自己编写一个数据适配器。

我首先是上网找轮子，随后想了一下，干脆自己包装了个简单的 `RecyclerViewAdapter` 先勉强用用。

将数据塞到图形界面上，最重要的三个东西是上下文、使用什么 `layout` 和数据本身，那么适配器的构造函数就需要接受这三项。然后就是平凡地重写 `RecyclerView.Adapter` 所要求的三个函数。不过这里我在重写 `onBindViewHolder` 的时候留了空，因为这里就是定义适配器最重要部分——数据如何映射到 UI 的函数。我将这一个函数设为虚函数，让子类继承重写就可以实现各种映射方式。最后的成果大致这样：

{% codeblock lang:java Java %}
public abstract class RecyclerViewAdapter<T> extends RecyclerView.Adapter<RecyclerViewAdapter.ViewHolder> {
    protected Context mContext;
    protected int mLayoutId;
    protected List<T> mData;

    public RecyclerViewAdapter(Context context, int layoutId, List<T> data) {
        this.mContext = context;
        this.mLayoutId = layoutId;
        this.mData = data;
    }

    @Override
    public @NonNull ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        return new ViewHolder(
                LayoutInflater.from(mContext).inflate(mLayoutId, parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        // TODO: What is this?
        // holder.updatePosition(position);
        this.convert(holder, mData.get(position));
    }

    public abstract void convert(ViewHolder holder, T data);

    @Override
    public int getItemCount() {
        return mData.size();
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {
        final private View mConvertView;

        public ViewHolder(View itemView) {
            super(itemView);
            this.mConvertView = itemView;
        }

        public <K extends View> K getViewById(int viewId) {
            return (K) mConvertView.findViewById(viewId);
        }
    }
}
{% endcodeblock %}

用起来也简单，传入三个要素，之后重写 `convert` 方法定义一下数据到 UI 的映射就好了：

{% codeblock lang:java Java %}
binding.entityList.setAdapter(new RecyclerViewAdapter<Entity>(
    HomeFragment.this.getActivity(), R.layout.home_entity_item, baseEntities
) {
    @Override
    public void convert(RecyclerViewAdapter.ViewHolder holder, Entity data) {
        ((TextView) holder.getViewById(R.id.entity_name)).setText(data.getLabel());
        ((TextView) holder.getViewById(R.id.entity_category)).setText(data.getCategory());
    }
});
{% endcodeblock %}

你会发现，这比 React Native 要写的东西多太多了。上面这么多需要折腾的， React Native 只需要这样写：

{% codeblock lang:typescript TypeScript %}
<View>
    {
        baseEntities.map((val, ind) => (
            <View key={ind}>
                <Text> {val.name} </Text>
                <Text> {val.category} </Text>
            </View>
        ))
    }
</View>
{% endcodeblock %}

就可以实现一个列表了。

Java 我写了一整个类，光数据转成 UI 也写了好几行长代码，这还没有算 `.xml` 文件。