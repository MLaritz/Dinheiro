# Dinheiro.GoogleAnalytics
ASP.NET MVC Helpers for Google Analytics.

Allows you to easily track page views, events, social actions and ecommerce by using action filter attributes or an easy API.

You can also install using [NuGet](http://nuget.org/):
<pre>
  PM> Install-Package Dinheiro.GoogleAnalytics
</pre>

Also, see the **Dinheiro.GoogleAnalytics.Example** MVC app for detailed examples.

## Usage:
In your application startup, be sure to set your Google Analytics Account ID:

	using Dinheiro.GoogleAnalytics;
	...
	GoogleAnalytics.Account = "UA-12345-6";

In your `_Layout.cshtml` file:

	@using Dinheiro.GoogleAnalytics
	<html>
	<head>
		...
		@GoogleAnalytics.Render()
	</head>
	<body>
		...
	</body>
	</html>

This will automatically output the required Google Analytics scripts to start tracking your page views:

	<script type="text/javascript">
		var _gaq = _gaq || [];
		_gaq.push(['_setAccount','UA-12345-6']);
		_gaq.push(['_trackPageview']);
		(function() {
  		var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
  		ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
  		var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);
		})();
	</script>

### Ecommerce orders
To track an ecommerce order, in your order complete action method, simply add the transaction and items in the order:

	public ActionResult OrderComplete()
	{
		var orderId = 123456;
		GoogleAnalytics.Current.AddTransaction(orderId, total: 18.98m, shipping: 3m);
		GoogleAnalytics.Current.AddItem("ProductSKU1", "Product name", 12.99m, 2, orderId);
		GoogleAnalytics.Current.AddItem("ProductSKU2", "Product name", 2.99m, 1, orderId);
		return View();
	}

### Tracking an event
Use the handy action filter:

	[GoogleAnalyticsEvent(Category = "Event Category", Action = "Event Action")]
	public ActionResult Index()
	{
		return View();
	}

You can even ask it to use values from query parameters:

	[GoogleAnalyticsEvent(CategoryParameter = "category", Action = "Event Action", LabelParameter = "label")]
	public ActionResult Index(string category, string label)
	{
		return View();
	}

Or call the API directly:

	public ActionResult Index()
	{
		GoogleAnalytics.Current.TrackEvent(category, action, label, value);
	}

### Tracking social actions
To track social actions, such as Facebook likes, tweets and so on:

	public ActionResult Like()
	{
		GoogleAnalytics.Current.TrackSocial("facebook", "like", "http://www.mysite.com");
	}
	
### Virtual page paths
If, instead of tracking your normal URLs, you need to track virtual page URLs, you can do so using an attribute:

	[GoogleAnalyticsPage("/virtualurl")]
	public ActionResult Index()
	{
		return View();
	}

Or by setting it directly:

	public ActionResult Index()
	{
		GoogleAnalytics.Current.VirtualPageUrl = "/virtualurl";
		return View();
	}
	
Note that this is optional - pages will be tracked using their normal URL if these are omitted.