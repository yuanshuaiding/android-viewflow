# View Flow for Android

ViewFlow is an Android UI widget providing a horizontally scrollable [ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html) with items populated from an [Adapter](http://developer.android.com/reference/android/widget/Adapter.html). Scroll down to the bottom of the page for a screen shot.

The component is a [Library Project](http://developer.android.com/guide/developing/eclipse-adt.html#libraryProject). This means that there's no need to copy-paste resources into your own project, simply add the viewflow component as a reference to any project.

## When to use
This library might be suitable if you have an indeterminate number of views in your viewflow, if instead you have a static numbers of views you ought to look at Fragments and the ViewPager in the Compatibility Library instead.

## Usage

### In your layout

    <org.taptwo.android.widget.ViewFlow
	    android:id="@+id/viewflow"
	    app:sidebuffer="5"
        />

The use of `app:sidebuffer` is optional. It defines the number of Views to buffer on each side of the currently shown View. The default sidebuffer is 3, making up a grand total of 7 (3 * 2 + 1) Views loaded at a time (at max).
To be able to use the more convenient `app:sidebuffer` attribute, the application namespace must be included in the same manner as the android namespace is. Please refer to the layout main.xml in the example project for a full example. Again, note that it's the application namespace and *not* the viewflow namespace that must be referred like `xmlns:app="http://schemas.android.com/apk/res/your.application.package.here"`.

### In your activity

    ViewFlow viewFlow = (ViewFlow) findViewById(R.id.viewflow);
    viewFlow.setAdapter(myAdapter);
    
Setting a different initial position (0 being default) is as easy as:

    viewFlow.setAdapter(myAdapter, 8);
    
Although possible, you should not call `setSelection(...)` immediately after calling `setAdapter(myAdapter)` as that might load unnecessary views giving you a decrease in performance.

### Listen on screen change events

If you need to listen to screen change events you would want to implement your own `ViewFlow.ViewSwitchListener` and pass it to the `setOnViewSwitchListener()` method.

    viewFlow.setOnViewSwitchListener(new ViewSwitchListener() {
        public void onSwitched(View v, int position) {
            // Your code here
        }
    });

### Listen on initialize view events

If you need a lazy View initialization you would want to implement your own `ViewFlow.ViewLazyInitializeListener` and pass it to the `setOnViewLazyInitializeListener()` method.

    viewFlow.setOnViewLazyInitializeListener(new ViewLazyInitializeListener() {
        public void onViewLazyInitialize(View view, int position) {
            // Your code here e.g.
            ((MyAdapter)((AbsListView)view).getAdapter()).initializeData();
        }
    });

### Flow Indicator
It is also possible to add a flow view indicator to your layout. The purpose of a `FlowIndicator` is to present a visual representation of where in the item list focus is at. You may either implement a `FlowIndicator` yourself or use an implementation provided by the View Flow library. The View Flow library currently supports the following indicators:

#### Circle Flow Indicator ####
This indicator shows a circle for each `View` in the adapter with a special circle representing the currently selected view (see screenshot below).

	<org.taptwo.android.widget.CircleFlowIndicator
		android:padding="10dip" android:layout_height="wrap_content"
		android:layout_width="wrap_content" android:id="@+id/viewflowindic"
		android:background="#00000000"/>

And then you'll need to connect your `ViewFlow` with the `FlowIndicator`:

	CircleFlowIndicator indic = (CircleFlowIndicator) findViewById(R.id.viewflowindic);
	viewFlow.setFlowIndicator(indic);
	
The following attributes are supported: `activeColor`, `inactiveColor`, `activeType` (either fill or stroke), `inactiveType` (either fill or stroke), `fadeOut` (time in ms until indicator fades out, 0 = never), `radius`.

#### Title Flow Indicator ####
This indicator presents the title of the previous, current and next `View` in the adapter (see screenshot below).

		<org.taptwo.android.widget.TitleFlowIndicator
			android:id="@+id/viewflowindic" android:layout_height="wrap_content"
			android:layout_width="fill_parent"
			app:footerLineHeight="2dp"
			app:footerTriangleHeight="10dp" app:textColor="#FFFFFFFF" app:selectedColor="#FFFFC445"
			app:footerColor="#FFFFC445" app:titlePadding="10dp" app:textSize="11dp" app:selectedSize="12dp"
			android:layout_marginTop="10dip"
			app:clipPadding="5dp" />

And then you'll need to connect your `ViewFlow` with the `FlowIndicator`:

		TitleFlowIndicator indicator = (TitleFlowIndicator) findViewById(R.id.viewflowindic);
		indicator.setTitleProvider(myTitleProvider);
		viewFlow.setFlowIndicator(indicator);

## Building a jar file
If you rather want a jar file instead of a including the project as an android library, run `ant jar` in the `android-viewflow/viewflow` folder, to build a jar file.

## Caveats ##
The manifest states a min sdk version of 4, which is true. But in any case you want to support an api level < 8 you will have to forward an `onConfigurationChanged` event to the `ViewFlow` from your `Activity`. I know this isn't a very nice solution, feel free to propose better ones!

		@Override
		public void onConfigurationChanged(Configuration newConfig) {
			super.onConfigurationChanged(newConfig);
			viewFlow.onConfigurationChanged(newConfig);
		}

## Contributions
The following persons deserves a mention for their contributions:

* Eric Taix
* Marc Reichelt, <http://marcreichelt.blogspot.com/>

### Want to contribute?

GitHub has some great articles on [how to get started with Git and GitHub](http://help.github.com/) and how to [fork a project](http://help.github.com/forking/).

Contributers are recommended to fork the app on GitHub (but don't have too). Create a feature branch, push the branch to git hub, press Pull Request and write a simple explanation.

One fix per commit. If let's say a commit closes the open issue 12. Just add `closes #12` in your commit message to close that issue automagically.

If you still feel uncomfortable contributing the project github-wise, don't hesistate to send a regular patch.

All code that is contributed must be compliant with [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).

## License
Copyright (c) 2011 [Patrik Åkerfeldt](http://about.me/pakerfeldt)

Licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

![ViewFlow for Android](https://github.com/pakerfeldt/android-viewflow/raw/master/viewflow-example/screen.png "ViewFlow for Android") &nbsp;&nbsp; ![ViewFlow for Android](https://github.com/pakerfeldt/android-viewflow/raw/master/viewflow-example/screen2.png "ViewFlow for Android")

## utils
public class AsyncImageLoader {
	private Map<String, SoftReference<Drawable>> imageCache = new HashMap<String, SoftReference<Drawable>>();

	public Drawable loadDrawable(final String imageUrl,
			final ImageCallback callback) {
		if (imageCache.containsKey(imageUrl)) {
			SoftReference<Drawable> softReference = imageCache.get(imageUrl);
			if (softReference.get() != null) {
				return softReference.get();
			}
		}
		final Handler handler = new Handler() {
			@Override
			public void handleMessage(Message msg) {
				callback.imageLoaded((Drawable) msg.obj);
			}
		};
		new Thread() {
			public void run() {
				Drawable drawable = loadImageFromUrl(imageUrl);
				imageCache.put(imageUrl, new SoftReference<Drawable>(drawable));
				handler.sendMessage(handler.obtainMessage(0, drawable));
			};
		}.start();
		return null;
	}

	protected Drawable loadImageFromUrl(String imgUrl) {
		URL myFileUrl = null;
		Bitmap bitmap = null;
		if (imgUrl != null && imgUrl.trim() != "") {
			try {
				myFileUrl = new URL(imgUrl);
			} catch (MalformedURLException e) {
				e.printStackTrace();
				return null;
			}
			try {
				HttpURLConnection conn = (HttpURLConnection) myFileUrl
						.openConnection();
				conn.setDoInput(true);
				conn.connect();
				InputStream is = conn.getInputStream();

				// xinff

				int length = (int) conn.getContentLength();

				if (length != -1) {
					byte[] imgData = new byte[length];
					byte[] temp = new byte[1024];
					int readLen = 0;
					int destPos = 0;

					while ((readLen = is.read(temp)) > 0) {
						System.arraycopy(temp, 0, imgData, destPos, readLen);
						destPos += readLen;
					}
					BitmapFactory.Options options = new BitmapFactory.Options();
					options.inJustDecodeBounds = true; 
					Bitmap bm = BitmapFactory.decodeByteArray(imgData, 0, destPos,
							options);
					options.inJustDecodeBounds = false;
					int be = options.outHeight / 200;
					if (be <= 0) {
						be = 2;
					}
					options.inSampleSize = be;
					bitmap = BitmapFactory.decodeByteArray(imgData, 0, destPos,
							options);
				}

				is.close();
			} catch (Exception e) {
				e.printStackTrace();
				System.out.println(e.getMessage());
				return null;
			}
			// Bitmap2Drawable
			Drawable d = new BitmapDrawable(bitmap);
			return d;
		}
		return null;
	}


	public interface ImageCallback {
		public void imageLoaded(Drawable imageDrawable);
	}

}

