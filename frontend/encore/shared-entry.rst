Creating a Shared Commons Entry
===============================

Suppose you have multiple entry files and *each* requires ``jquery``. In this
case, *each* output file will contain jQuery, slowing down your user's experience.
In this case, you can *extract* these common libraries to a "shared" entry file
that's included on every page:

.. code-block:: javascript

    Encore
        // ...
        .addEntry('page1', 'assets/js/page1.js')
        .addEntry('page2', 'assets/js/page2.js')

        // this creates a 'vendor.js' file with jquery and the bootstrap JS module
        // these modules will *not* be included in page1.js or page2.js anymore
        .createSharedEntry('vendor', [
            'jquery',
            'bootstrap',

            // you can also extract CSS - this will create a 'vendor.css' file
            // this CSS will *not* be included in page1.css or page2.css anymore
            'bootstrap-sass/assets/stylesheets/_bootstrap.scss'
        ])

As soon as you make this change, you need to include two extra JavaScript files
on your page before any other JavaScript file:

.. _encore-shared-entry-script:

.. code-block:: twig

    <!-- these two files now must be included in every page -->
    <script src="{{ asset('build/manifest.json') }}"></script>
    <script src="{{ asset('build/vendor.js') }}"></script>

    <!-- here you link to the specific JS files needed by the current page -->
    <script src="{{ asset('build/app.js') }}"></script>

    <!-- if you extracted some CSS, include vendor.css -->
    <link rel="stylesheet" href="{{ asset('build/vendor.css') }}" />

The ``vendor.js`` file contains all the common code that has been extracted from
the other files, so it's obvious that it must be included. The other file (``manifest.json``)
is less obvious: it's needed so that Webpack knows how to load those shared modules.

.. tip::

    The ``vendor.js`` file works best when its contents are changed *rarely*
    and you're using :ref:`long-term caching <encore-long-term-caching>`. Why?
    If ``vendor.js`` contains application code that *frequently* changes, then
    (when using versioning), its filename hash will frequently change. This means
    your users won't enjoy the benefits of long-term caching for this file (which
    is generally quite large).
