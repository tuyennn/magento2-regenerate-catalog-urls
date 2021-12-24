# What does it do
This extension adds console commands to be able to regenerate;

- a product rewrite URL based on its url path;
- a category rewrite URL based on its url path;
- a CMS page rewrite URL based on its url path;
- a category URL path based on its URL key and its parent categories.

# Install
Using Composer;

```sh
composer require elgentos/regenerate-catalog-urls
php bin/magento setup:upgrade
```

Or download and copy the `Iazel` directory into `app/code/`, enable the module and run `php bin/magento setup:upgrade`.

# How to use
```
Usage:
 regenerate:product:url [-s|--store="..."] [pids1] ... [pidsN]
 regenerate:category:url [-s]--store="..."] [cids1] ... [cidsN]
 regenerate:category:path [-s]--store="..."] [cids1] ... [cidsN]
 regenerate:cms-page:url [-s]--store="..."] [pids1] ... [pidsN]

Arguments:
 pids                  Products (or Pages) to regenerate
 cids                  Categories to regenerate

Options:
 --store (-s)          Use the specific Store View (default: 0)
 --help (-h)           Display this help message
```

Eg:
```sh
# Regenerate url for all products and the global store
php bin/magento regenerate:product:url

# Regenerate url for products with id (1, 2, 3, 4) for store 1
php bin/magento regenerate:product:url -s1 1 2 3 4
```

# FAQ

## What's the difference between url_key and url_path?
`url_key` contains the key, like `joust-duffle-bag` for the product "Joust Duffle Bag". The `url_path` is generated by taking the `url_key` and adding the suffix (which for products is stored in `catalog/seo/product_url_suffix` and defaults to `.html`). So the `url_path` would by default become `joust-duffle-bag.html`. It also adds the category slugs of the parent categories so the `url_path` might become `bags/joust-duffle-bag.html`. However, the use of `url_path` has been deprecated since early Magento 2.1 versions (see [here](https://github.com/magento/magento2/issues/9113)). If you are running on a recent Magento 2 version, you can safely delete those values by running `DELETE FROM catalog_product_entity_varchar WHERE attribute_id = (SELECT attribute_id FROM eav_attribute WHERE attribute_code = 'url_path' AND entity_type_id = 4)`.

URL paths are still used in categories though, so don't remove those.

## Why am I getting a 'Duplicated url' warning when running the command?
If you see this error, you have duplicate `url_key` values (within a store) in `catalog_product_entity_varchar`. You can use this extension to check those (and you need to fix them manually): [baldwin/magento2-module-url-data-integrity-checker](https://github.com/baldwin-agency/magento2-module-url-data-integrity-checker).
