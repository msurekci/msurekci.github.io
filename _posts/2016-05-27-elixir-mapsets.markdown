---
layout: post
title:  "Elixir - Mapsets"
date:   2016-05-27
categories: software development, elixir, mapset, functional programming
---

While writing some [koans](https://github.com/elixirkoans/elixir-koans) for MapSet I discovered some new information about it and thought I would share some of them here.

MapSet is another type of collection like lists and maps. What makes it unique is it doesn't allow duplication. 

If we did: 
{% highlight elixir %}
set = MapSet.new([1, 1, 1, 2])
MapSet.size(set)
{% endhighlight %} 

The result is 2.

The element 1 will only appear in there once and no more.

### Ordered or unordered?

Another interesting thing about MapSets is that it is unordered which is true to a certain extent. MapSets are actually [Hash array mapped trie](https://en.wikipedia.org/wiki/Hash_array_mapped_trie). To a certain point
it is ordered. In the case of Elixir MapSet it is up to 32 elements.

Example in IEx: 
{% highlight elixir %}
1..10 |> MapSet.new |> Enum.fetch(0)
{:ok, 1}
{% endhighlight %}

{% highlight elixir %}
1..33 |> MapSet.new |> Enum.fetch(0)
{:ok, 11}
{% endhighlight %}

### Performance
MapSet are really fast and is really useful when searching for something. MapSets can check existence in O(log(n)) time. Here are some stats: [MapSet evaluation](https://gist.github.com/lexmag/32977ce8fd7cb44ddefa). 

_Note that HashSet is deprecated in the later versions of Elixir_

You may want to consider using a MapSet rather than a List when searching through a collection depending on your use case.

