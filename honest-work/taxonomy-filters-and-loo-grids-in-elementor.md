Elementor Loop Grid and Taxonomy filters only work right when you add them in the template
rather than importing the template.

If your typical flow is to have a saved template like section or page, and you import those
templates in your page, the filters break.

The only way to make them work right is to:
- Add the loop grid in page
- Add the filters in Page.
- Configure both.


**Note:** This has been a long standing bug and it hasn't been fixed yet!
