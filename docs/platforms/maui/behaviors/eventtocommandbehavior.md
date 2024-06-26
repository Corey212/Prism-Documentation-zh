# Using the EventToCommandBehavior

The `EventToCommandBehavior` class provides a convenient way to, in XAML, "bind" events to `ICommand` according to MVVM paradigm to avoid code behind.

## Properties

The `EventToCommandBehavior` expose the following properties

* **EventName** The name of the event to listen to. For example _ItemTapped_
* **Command** The `ICommand` that will be executed when the event is raised
* **CommandParameter** The parameter that will be sent to the `ICommand.Execute(object)` method
* **EventArgsConverter** Instance of `IValueConverter` that allows operating on the `EventArgs` type for the *EventName*
* **EventArgsConverterParameter** The parameter that will be sent as the _parameter_ argument to `IValueConverter.Convert` method
* **EventArgsParameterPath** Parameter path to extract property from `EventArgs` that will be passed to `ICommand.Execute(object)`

## Usage

First declare the namespace and assembly in where `EventToCommandBehavior` is declared and declare a XML-namespace.

`xmlns:prism="http://prismlibrary.com"`

### CommandParameter

Bind or declare a parameter that will be sent to the `ICommand.Execute(object)` method.

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MyNamespace.ContentPage"
             xmlns:prism="http://prismlibrary.com">
  <ListView>
    <ListView.Behaviors>
      <prism:EventToCommandBehavior EventName="ItemTapped"
                                    Command="{Binding ItemTappedCommand}"
                                    CommandParameter="MyParameter" />
    </ListView.Behaviors>
  </ListView>
</ContentPage>
```

### EventArgsConverter

Using the **EventArgsConverter** to retrieve the `ItemTappedEventArgs.Item` property.

```c#
using System.Globalization;

namespace Prism.Converters;

public class ItemTappedEventArgsConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        var itemTappedEventArgs = value as ItemTappedEventArgs;
        if (itemTappedEventArgs == null)
        {
            throw new ArgumentException("Expected value to be of type ItemTappedEventArgs", nameof(value));
        }
        return itemTappedEventArgs.Item;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        throw new NotImplementedException();
    }
}

```

The XAML need a reference to the converter and the converter resource need to be defined

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MyNamespace.ContentPage"
             xmlns:prism="http://prismlibrary.com"
             xmlns:c="clr-namespace:AwesomeApp.Converters;assembly=YourProject">
  <ContentPage.Resources>
    <ResourceDictionary>
      <c:ItemTappedEventArgsConverter x:Key="itemTappedEventArgsConverter" />
    </ResourceDictionary>
  </ContentPage.Resources>
  <ListView>
    <ListView.Behaviors>
      <prism:EventToCommandBehavior EventName="ItemTapped"
                                    Command="{Binding ItemTappedCommand}"
                                    EventArgsConverter="{StaticResource itemTappedEventArgsConverter}" />
    </ListView.Behaviors>
  </ListView>
</ContentPage>
```

### EventArgsParameterPath

Attach the command to **ItemTapped** event will raise the `ItemTappedEventArgs` event.

```c#
public class ItemTappedEventArgs : EventArgs
{
    public object Item { get; }
    public object Group { get; }
}
```

Setting `EventArgsParameterPath` to **Item** will extract the property value and pass it to the `ICommand.Execute(object)` method

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MyNamespace.ContentPage"
             xmlns:prism="http://prismlibrary.com">
  <ListView>
    <ListView.Behaviors>
      <prism:EventToCommandBehavior EventName="ItemTapped"
                                    Command="{Binding ItemTappedCommand}"
                                    EventArgsParameterPath="Item" />
    </ListView.Behaviors>
  </ListView>
</ContentPage>
```
