[![Build Status](https://travis-ci.org/WeltN24/WidgetAdapter.svg?branch=master)](https://travis-ci.org/WeltN24/WidgetAdapter)
[![Maven Central](https://img.shields.io/maven-central/v/de.welt/widgetAdapter.svg)](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22de.welt%22%20AND%20a%3A%widgetAdapter%22)
# WidgetAdapter
This is a small library that eases the management of adapters for ```RecyclerView```s especially if you use different item view types. It is written in Kotlin and intended to be used in Kotlin since it relies on some of its syntactic sugar.
## Install
Release versions:

```groovy
repositories {
    mavenCentral()
}

dependencies{
    compile "de.welt:widgetAdapter:0.2"
}
```

Snapshot versions:

```groovy
repositories {
    maven { url 'https://oss.sonatype.org/content/repositories/snapshots' }
}

dependencies{
    compile "de.welt:widgetAdapter:0.3-SNAPSHOT"
}
```

## Usage
All you need for the WidgetAdapter is a ```LayoutInflater``` and a set of providers that instantiate your ```Widget```s. ```Widget```s are your views that are wrapped by implementations of the ```Widget<T>``` interface.
### Create a WidgetAdapter

```kotlin
val adapter = WidgetAdapter(LayoutInflater.from(context))
adapter.addWidget { ListItemWidget() }        
adapter.addWidget { SeparatorWidget() }        
recyclerView.adapter = adapter
```

Of course it would be a good practise to inject the adapter and the providers through the DI mechanism of your choice. 
### Create a Widget
Your item views have to be mapped into ```Widget```s for the ```WidgetAdapter``` to match data types and view types. It uses ```setData(data: T)``` to update the data of a ```Widget```. Keep in mind, that your ```Widget``` might have been recycled and you might have to clean your view from its previous state.

```kotlin
class YourWidget: Widget<String>(R.layout.list_item){
    private lateinit var textView: TextView
    
    override fun onViewCreated(view: View) {
        textView = view.findViewById(R.id.text_view)
    }

    override fun setData(data: String) {
        textView.text = data
    }
}
```

For the implementation of your ```Widget```s you are free to use your preferred UI library like DataBinding, kotterknife and the like. If you need to control the inflation you can override the default implementation of `createView(inflater: LayoutInflater, parent: ViewGroup?): View`. The `Widget` class also provides you with some small helper functions.

```kotlin
class YourWidget: Widget<String>(R.layout.your_item_layout){

    private val textView: TextView by viewId(R.id.text_view)
    private val imageView by viewId<ImageView>(R.id.image_view)
    
    private val string: String by resourceId(R.string.your_string)
    private val intArray by resourceId<Array<Int>>(R.string.your_string)
    private val color by colorResource(R.color.colorPrimary)
    private val dimension by dimensionInPixels(R.dimen.activity_horizontal_margin)

    override fun setData(data: String) {
        textView.text = data
    }
}
```
