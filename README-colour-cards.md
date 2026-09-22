# Collection colour cards

This Dawn 16.0.0 change is limited to `sections/main-collection-product-grid.liquid`, `snippets/card-product.liquid`, and `snippets/price.liquid`. It reuses Dawn's existing CSS and JavaScript.

## Behaviour

- Recognises an option named `Color` or `Colour`, case-insensitively, in any option position.
- Renders one card per distinct colour, not one per colour/size combination. Products without that option keep their original card.
- Uses the first available variant of each colour, falling back to its first variant when sold out. The displayed price, compare-at price, availability, unit price and destination all belong to that representative variant. Different sizes can have different prices; this is the exact linked variant's price, not a colour-wide price range.
- Uses the representative variant's image, then another variant image of the same colour, then the product image. Assign colour images in Shopify Admin for accurate results. The generic secondary hover image is suppressed on colour cards because it can show another colour.
- Appends the colour to the product title and uses Shopify's variant URL.
- Retains Dawn's responsive grid, image ratios, vendor, ratings, badges, animations, lazy loading, sorting and AJAX filtering. Active native Color/Colour option filters limit displayed colours. Other filters and sort order retain Shopify's product-level semantics.
- Standard quick add opens the corresponding selected variant with Dawn's normal option chooser. Bulk quick add retains Dawn's product-wide order list, shared between a product's colour cards to avoid duplicate list IDs.
- Pagination and result counts remain product-based. `Products per page` controls parent products; the number of visible cards can be larger.
- Other consumers of the shared card and price snippets retain their defaults.

## Preview in a development store

Install Shopify CLI if needed, then run from this project only:

```sh
npm install -g @shopify/cli@latest
cd shopify-dawn-variant-collection
shopify theme check
shopify theme dev --store YOUR-STORE.myshopify.com
```

Sign in with an account allowed to develop themes in that store. Open the preview URL printed by the CLI and navigate to `/collections/YOUR-COLLECTION-HANDLE`. Keep the command running for live updates. `theme dev` uses a development theme; publishing is not required.

Ensure the collection has products available to the Online Store channel, a Color/Colour option, assigned variant images, and prices. The collection template must use the main collection product grid section.

Official CLI documentation: https://shopify.dev/docs/api/shopify-cli/theme/theme-dev

## Local validation

Shopify CLI Theme Check inspected 155 files with zero errors and nine warnings. Running the same check against the original three files produced the same nine warnings; this change introduces none. The collection section schema also parses as valid JSON. Store rendering and cart interactions have not been tested locally.

## Store verification checklist

1. Test a product with two colours and multiple sizes: expect two cards, each linking to its own `?variant=` URL with the correct selected colour.
2. Test Color/Colour as the first, second and third option; also test a product with no colour option.
3. Verify sale prices, different prices by colour, a colour with every size sold out, and one whose first size is sold out but another is available.
4. Test missing variant images and a product with no images.
5. Test desktop/mobile layouts, colour filters, filter reset, sorting and page navigation.
6. Enable standard quick add and verify the initial colour and cart variant. Enable bulk quick add and verify that each colour card opens the product's shared bulk order list and quantity updates work.
7. Check another section that uses product cards, such as featured collection, for unchanged behaviour.

Shopify-hosted visual and runtime checks must be completed in a development store. No store credentials are included.

## Large catalog limitation

Shopify exposes at most 250 variants through an unpaginated `product.variants` array. This implementation also tries the option value's `variant` when a colour is outside that array, but this can be unavailable for some combinations of selected options. Products exceeding 250 variants need a separate high-variant implementation and store testing before relying on complete colour coverage.

Reference: https://shopify.dev/docs/storefronts/themes/product-merchandising/variants/support-high-variant-products
