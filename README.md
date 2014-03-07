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

&lt;select id=&quot;product-select&quot; name=&quot;id&quot; class=&quot;hidden&quot;&gt;<br />
{% for variant in product.variants %}<br />
&lt;option value=&quot;{{ variant.id }}&quot;&gt;{{ variant.title }} - {{ variant.price | money }}&lt;/option&gt;<br />
{% endfor %}<br />
&lt;/select&gt;<br />

and replace it with {% include 'wholesale' %}.

If you also want to update the displayed product price when a customer selects an option from the variants dropdown menu, you can integrate the code from the wholesale-with-price.liquid file in to your product.liquid template. Replace your existing variants select object with lines 17-25. Then replace where your template is currently displaying the product price with lines 1-15. Then, add lines 27-35, the javascript function to the template footer or your theme.liquid footer.

<hr />

TROUBLESHOOTING

If you're having trouble, check to see if your theme employs <a href="http://docs.shopify.com/support/your-website/themes/can-i-make-my-theme-use-products-with-multiple-options">Shopify's multiple option JavaScript helper</a>. You can tell if you find this code in your theme.liquid header:

{{ 'option_selection.js' | shopify_asset_url | script_tag }}

You'll need to delete this link. Then remove the callback and instantiation of Shopify.OptionSelectors from either the theme.liquid or product.liquid template. You can remove these by deleting the entire jquery script that looks something like this:

&lt;script&gt;
<br />// &lt;![CDATA[  
<br />var selectCallback = function(variant, selector) {
<br />if (variant) {
<br />if (variant.available) {
<br />$('#add').removeClass('disabled').removeAttr('disabled').val('Add to Cart').fadeTo(200,1);
<br />} else {
<br />$('#add').val('Sold Out').addClass('disabled').attr('disabled', 'disabled').fadeTo(200,0.5);        
<br />}
<br />if ( variant.compare_at_price &gt; variant.price ) {
<br />$('#price-field').html('&lt;span class=&quot;product-price on-sale&quot;&gt;'+ Shopify.formatMoney(variant.price, &quot;&quot;) +'&lt;/span&gt;'+'&amp;nbsp;&lt;s class=&quot;product-compare-price&quot;&gt;'+Shopify.formatMoney(variant.compare_at_price, &quot;&quot;)+ '&lt;/s&gt;');
<br />} else {
<br />$('#price-field').html('&lt;span class=&quot;product-price&quot;&gt;'+ Shopify.formatMoney(variant.price, &quot;&quot;) + '&lt;/span&gt;' );
<br />}        
<br />} else {
<br />$('#add').val('Unavailable').addClass('disabled').attr('disabled', 'disabled').fadeTo(200,0.5);
<br />}
<br />}
<br />jQuery(function($) {
<br />new Shopify.OptionSelectors(&quot;product-select&quot;, { product: , onVariantSelected: selectCallback });
<br />});
<br />// ]]&gt;
<br />&lt;/script&gt;