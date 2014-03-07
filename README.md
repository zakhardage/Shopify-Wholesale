Shopify Wholesale
=================

Shopify Wholesale

Adding wholesale functionality to your Shopify requires 4 steps:

1. Change your Checkout &rarr; Settings &rarr; Customer Accounts so that "Accounts are optional"
![](https://raw.github.com/zakhardage/shopify-wholesale/master/images/settings-checkout.png)

2. Add the 'wholesale' tag to customer tags
![](https://raw.github.com/zakhardage/shopify-wholesale/master/images/customer-tags.png)

3. Add wholesale variants &mdash; add another for each of your existing product variants with "wholesale" in the title
![](https://raw.github.com/zakhardage/shopify-wholesale/master/images/product-variants.png)

4. Modify the product.liquid template 

The simplest way is to add wholesale.liquid to your theme's snippets folder. Then, find this code (or something similar) in your product.liquid template:

bq. &lt;select id=&quot;product-select&quot; name=&quot;id&quot; class=&quot;hidden&quot;&gt;
{% for variant in product.variants %}
&lt;option value=&quot;{{ variant.id }}&quot;&gt;{{ variant.title }} - {{ variant.price | money }}&lt;/option&gt;
{% endfor %}
&lt;/select&gt;

and replace it with {% include 'wholesale' %}.

If you also want to update the displayed product price when a customer selects an option from the variants dropdown menu, you can integrate the code from the wholesale-with-price.liquid file in to your product.liquid template. Replace your existing variants select object with lines 17-25. Then replace where your template is currently displaying the product price with lines 1-15. Then, add lines 27-35, the javascript function to the template footer or your theme.liquid footer.