Shopify Wholesale
=================

Adding wholesale functionality to your Shopify requires 4 steps:

1. Change your Checkout &rarr; Settings &rarr; Customer Accounts so that "Accounts are optional"
![](https://raw.github.com/zakhardage/shopify-wholesale/master/images/settings-checkout.png)

2. Add the 'wholesale' tag to customer tags
![](https://raw.github.com/zakhardage/shopify-wholesale/master/images/customer-tags.png)

3. Add wholesale variants &mdash; add another for each of your existing product variants with "wholesale" in the title
![](https://raw.github.com/zakhardage/shopify-wholesale/master/images/product-variants.png)

4. Modify the product.liquid template 

The simplest way is to add wholesale.liquid to your theme's snippets folder. Then, find this code (or something similar) in your product.liquid template:

       <select id="product-select" name="id" class="hidden">
       {% for variant in product.variants %}
         <option value="{{ variant.id }}">{{ variant.title }} - {{ variant.price | money }}</option>
       {% endfor %}
       </select>


and replace it with {% include 'wholesale' %}.

If you also want to update the displayed product price when a customer selects an option from the variants dropdown menu, you can integrate the code from the wholesale-with-price.liquid file in to your product.liquid template. Replace your existing variants select object with lines 34-42. Then replace where your template is currently displaying the product price with lines 1-32. Then, add lines 44-52, the javascript function to the template footer or your theme.liquid footer.

<hr />

TROUBLESHOOTING

If you're having trouble, check to see if your theme employs <a href="http://docs.shopify.com/support/your-website/themes/can-i-make-my-theme-use-products-with-multiple-options">Shopify's multiple option JavaScript helper</a>. You can tell if you find this code in your theme.liquid header:

{{ 'option_selection.js' | shopify_asset_url | script_tag }}

You'll need to delete this link. Then remove the callback and instantiation of Shopify.OptionSelectors from either the theme.liquid or product.liquid template. You can remove these by deleting the entire jquery script that looks something like this:

		<script>
	// <![CDATA[  
	var selectCallback = function(variant, selector) {
	  if (variant) {
	    if (variant.available) {
	      // Selected a valid variant that is available.
	      $('#add').removeClass('disabled').removeAttr('disabled').val('Add to Cart').fadeTo(200,1);
	    } else {
	      // Variant is sold out.
	      $('#add').val('Sold Out').addClass('disabled').attr('disabled', 'disabled').fadeTo(200,0.5);        
	    }
	    // Whether the variant is in stock or not, we can update the price and compare at price.
	    if ( variant.compare_at_price > variant.price ) {
	      $('#price-field').html('<span class="product-price on-sale">'+ Shopify.formatMoney(variant.price, "") +'</span>'+'&nbsp;<s class="product-compare-price">'+Shopify.formatMoney(variant.compare_at_price, "")+ '</s>');
	    } else {
	      $('#price-field').html('<span class="product-price">'+ Shopify.formatMoney(variant.price, "") + '</span>' );
	    }        
	  } else {
	    // variant doesn't exist.
	    $('#add').val('Unavailable').addClass('disabled').attr('disabled', 'disabled').fadeTo(200,0.5);
	  }
	}
	// initialize multi selector for product
	jQuery(function($) {
	  new Shopify.OptionSelectors("product-select", { product: , onVariantSelected: selectCallback });
	});
	// ]]>
	</script>
	
<hr />

QUANTITY MINIMUMS

1. Add the fields from the settings_schema.json file to your theme's settings_schema.json file

2. Enter the minimum wholesale product quantities in your theme settings separated by a comma in this format type:qty

1. Replace <form action="/cart/add" method="post"> with <form action="/cart/add" method="post" {% if minimum == true %}onsubmit="return qtyCheck()"{% endif %}>
	
2. Above the opening form tag, add:
	{% if settings.qty-min contains product.type %}
		{% assign qty-min = settings.qty-min | split:product.type %}
		{% assign qty-min = qty-min[1] %}
		{% if qty-min contains ',' %}{% assign qty-min = qty-min | split:',' %}{% assign qty-min = qty-min[0] %}{% endif %}
		{% assign minimum = true %}
	{% endif %}

3. If there is an input field in the form, replace it with this <input type="number" name="quantity" class="quantity" value="{% if minimum %}{{ qty-min }}{% else %}1{% endif %}" />

4. Add this script to the bottom of the page:
	{% if minimum %}
		<script>
			function qtyCheck() {
				var qty = $('.quantity').val();
				if(qty < {{ qty-min | minus:1 }}) {
					alert("The minimum quantity for wholesale {{ product.title }} is {{ qty-min }}.")
					return false;
				}
			}
		</script>
	{% endif %}


I'll update this later to include multiple option selectors, and cart minimums.
