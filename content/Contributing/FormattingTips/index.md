+++
chapter = true
pre = "<b><i class='fas fa-clone'></i> </b>"
title = "Formatting tips"
weight = 100
+++

# Tips and tricks in terms of working with MarkDown and Hugo

## Markdown Cheat Sheet
Here you can find a rather interesting Cheat Sheet regarding markdown: [https://www.markdownguide.org/cheat-sheet](https://www.markdownguide.org/cheat-sheet)

## Code Fences / Syntax highlighting

The syntax to use codefences is with backticks.  If you provide the language after the first block of backticks, github will automatically put that in decent syntax highlighting.  So, A simple code fence with AL code, can simply be done by:
````
```AL
  procedure ALGuidelinesRock()
  var
    Customer: Record Customer;
  begin
    Customer.Get('10000');
    Customer.Name := 'waldo';
    Customer.Modify(true);
  end;
```
````

Results in: 
```AL
  procedure ALGuidelinesRock()
  var
    Customer: Record Customer;
  begin
    Customer.Get('10000');
    Customer.Name := 'waldo';
    Customer.Modify(true);
  end;
```

## Hugo Shortcodes
Since we're using "Hugo", we can use it's shortcode.  Here is a reference: [https://gohugo.io/content-management/shortcodes/](https://gohugo.io/content-management/shortcodes/)

It basically means we are able to use easy notations to do cool things.  Let's point out some useful shortcodes:

### Twitter

```
{{</* tweet user="waldo1001" id="1458787011024805892" */>}}
```
makes:
{{< tweet user="waldo1001" id="1458787011024805892" >}}

### YouTube
```
{{</* youtube QVOMCYitLEc */>}}
```
makes: 
{{< youtube QVOMCYitLEc >}}

### Figure
```
{{</* figure src="http://www.waldo.be/wp-content/uploads/2021/11/business-central-logo.png" title="Business Central" */>}}
```
makes:
{{< figure src="http://www.waldo.be/wp-content/uploads/2021/11/business-central-logo.png" title="Business Central" >}}
