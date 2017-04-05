## Starter Project for Photo Map Exercise (Swift)

### Should be:
![Image](http://i.imgur.com/WIwqNtn.gif)

### My walkthrough:
![Image](https://github.com/sammanthp007/Photo-Map/blob/master/walkthrough.gif)

### Resources
- [Vimeo Video](https://vimeo.com/156644693/5fb0a0c959)

### Need to complete
- Connects with Foursquare API
- Implements `LocationsViewController`
- Placeholders for `PhotoMapViewController` and `FullImageViewController`

# Photo Map
Today we'll be building a photo map. It will allow the user to take a photo, tag it with a location, and then see a map with all the previously tagged photos.

## Milestone 1: Setup
- Download the [starter
  project](https://github.com/codepath/ios_photo_map/archive/master.zip). The
  starter project contains the following view controllers:
    - `PhotoMapViewController` => This is where you'll add the map in Milestone
      #2.
    - `LocationsViewController` => This is already implemented and allows you
      to search Foursquare for a location that you want to drop a photo.
    - `FullImageViewController` => This is where you'll add a fullscreen image
      in **Bonus #2.**
- Fill in the following constants in `LocationsViewController` to connect to the Foursquare API:
    - `CLIENT_ID` = QA1L0Z0ZNA2QVEEDHFPQWK0I5F1DE3GPLSNW4BZEBGJXUCFL
    - `CLIENT_SECRET` = W2AOE1TYC4MHK5SZYOUGX0J3LVRALMPB4CXT3ZH21ZCPUMCU

## Milestone 2: Create the Map View

![Image](http://i.imgur.com/tro9qJv.gif)

- Implement the PhotoMapViewController to display a map of San Francisco with a
  camera button overlay.
    - [Add the MapKit
      framework](http://guides.codepath.com/ios/Project-Frameworks#adding-frameworks-to-project)
      to the Build Phases.
    - Then import `MapKit` into `PhotoMapViewController`.
    - Add the MKMapView and the UIButton for displaying the camera (asset
      included in the starter project).
    - Create an outlet for the map view.
    - [Set initial
      visible](http://guides.codepath.com/ios/Using-MapKit#centering-a-mkmapview-at-a-point-with-a-displayed-region)
      region of the map view to San Francisco:


## Milestone 3: Take a Photo
1. Create a property in your PhotoMapViewController to store your picked image
```
var pickedImage: UIImage!
```
2. Pressing the camera button should modally [present the
   camera](http://guides.codepath.com/ios/Camera-Quickstart).
    - NOTE: The simulator does not support using the actual camera. Check that
      the source type is indeed available by using
      `UIImagePickerController.isSourceTypeAvailable(.camera)` which will
      return true if the device supports the camera. [See the note here for an
      example](http://guides.codepath.com/ios/Camera-Quickstart#step-2-instantiate-a-uiimagepickercontroller)
3. When the user has chosen an image, in your [delegate
   method](http://guides.codepath.com/ios/Camera-Quickstart#step-3-implement-the-delegate-method)
   you'll want to:
    1. Assign the picked image to the pickedImage property
    2. Dismiss the modal camera view controller you previously presented. (This time we will expand the completion closure instead of setting it to nil in order to perform a segue to the LocationsViewController in the next step.)
    3. [Launch the LocationsViewController](http://guides.codepath.com/ios/Using-Modal-Transitions#triggering-the-transition-manually) in the completion block of dismissing the camera modal using the segue identifier `tagSegue`.

## Milestone 4: Tag a Location

Most of `LocationsViewController` is already implemented for you. There are 3
remaining pieces you'll need to implement:

1. Create a `LocationsViewControllerDelegate` so that `LocationsViewController` can communicate back to `PhotoMapViewController` the location that was selected.
```
// Protocol definition - top of LocationsViewController.swift
protocol LocationsViewControllerDelegate : class {
   func locationsPickedLocation(controller: LocationsViewController, latitude: NSNumber, longitude: NSNumber)
}

// LocationsViewController's delegate variable
class LocationsViewController: … {
   weak var delegate : LocationsViewControllerDelegate!
   ...
}   
```

2. Set the delegate from the **prepare(for:​sender:​)**
   AKA(prepareForSegue) method of `PhotoMapViewController`.
    - NOTE: The starter project may have the older syntax of the
      prepareForSegue method. Use auto complete to create and override the
      prepare(for:​sender:​) method.
```
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
   let locationsViewController = segue.destination as! LocationsViewController
   locationsViewController.delegate = self
}
```

- After setting the delegate, you will get a compiler error saying 
![Image](http://i.imgur.com/6qLP2xZ.png)

* In your `PhotoMapViewController` class declaration, declare that you will implement the required methods to conform to the `LocationsViewControllerDelegate`.
```
class PhotoMapViewController: UIViewController, ... LocationsViewControllerDelegate
```

1. In the
[didSelectRowAtIndexPath](https://developer.apple.com/reference/uikit/uitableviewdelegate/1614916-tableview)
inside `LocationsViewController` call the `locationsPickedLocation` delegate
method and pass in the latitude and longitude of the location the user selects
as arguments.
```
delegate.locationsPickedLocation(controller: self, latitude: lat, longitude: lng)

// Return to the PhotoMapViewController
navigationController?.popViewController(animated: true)
```

## Milestone 5: Drop a Pin on the map

![Image](http://i.imgur.com/Ih8wIo9.gif)

1. Add a pin to the map (We won't be actually using our image yet)
    - In the PhotoMapViewController, inside the
      locationsPickedLocation(controller:latitude:longitude:) method, [Add a
      pin to the
      map](http://guides.codepath.com/ios/Using-MapKit#drop-pins-at-locations)
        - You can set the title to the longitude using `annotation.title =
          String(describing: latitude)`
        - Notice how we call the
          [addAnnotation](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKMapView_Class/index.html#//apple_ref/occ/instm/MKMapView/addAnnotation:)
          method on our `mapView` instance:
        - NOTE: The addAnnotation method expects an
          [MKAnnotation](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKAnnotation_Protocol/index.html#//apple_ref/swift/intf/c:objc(pl)MKAnnotation)
          (which is a protocol). If all you need is a point on a map, you can
          use MKPointAnnotation (which is a concrete implement of
          MKAnnotation).

## Milestone 6: Add the photo you chose in the annotation view

![Image](http://i.imgur.com/jsPJ3er.gif)
1. [add a custom image to the annotation
   view](http://guides.codepath.com/ios/Using-MapKit#use-custom-images-for-map-annotations)

    1. Set the PhotoMapViewController as MapView's delegate
    2. Add `MKMapViewDelegate` to your PhotoMapViewController's class declaration.
    3. Implement the
       [mapView:viewForAnnotation](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKMapView_Class/index.html#//apple_ref/occ/instm/MKMapView/viewForAnnotation:)
       delegate method to provide an annotation view. The code below will add
       your `pickedImage` to the annotation view.
```
func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) -> MKAnnotationView? {
    let reuseID = "myAnnotationView"

    var annotationView = mapView.dequeueReusableAnnotationView(withIdentifier: reuseID)
    if (annotationView == nil) {
        annotationView = MKPinAnnotationView(annotation: annotation, reuseIdentifier: reuseID)
        annotationView!.canShowCallout = true
        annotationView!.leftCalloutAccessoryView = UIImageView(frame: CGRect(x:0, y:0, width: 50, height:50))
    }

    let imageView = annotationView?.leftCalloutAccessoryView as! UIImageView
    // Add the image you stored from the image picker
    imageView.image = pickedImage

    return annotationView
}
```

## Bonus 1: Implement a Custom MKAnnotation
![Image](http://i.imgur.com/FEgTygn.gif)

- If we want a preview of the photo in the annotation view (and not just the
  camera icon), we need a way to keep track of which annotation corresponds to
  which photo.
- One way to do this, is to create a class that implements the MKAnnotation
  protocol to keep track of the photo:
```
class PhotoAnnotation: NSObject, MKAnnotation {
    var coordinate: CLLocationCoordinate2D = CLLocationCoordinate2DMake(0, 0)
    var photo: UIImage!

    var title: String? {
        return "\(coordinate.latitude)"
    }
}
```
- With the PhotoAnnotation, you can now set the
  [leftCalloutAccessoryView](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKAnnotationView_Class/index.html#//apple_ref/occ/instp/MKAnnotationView/leftCalloutAccessoryView)
  image to the `photo`. Use the following code to resize the image beforehand:
```
var resizeRenderImageView = UIImageView(frame: CGRectMake(0, 0, 45, 45))
resizeRenderImageView.layer.borderColor = UIColor.whiteColor().CGColor
resizeRenderImageView.layer.borderWidth = 3.0
resizeRenderImageView.contentMode = UIViewContentMode.ScaleAspectFill
resizeRenderImageView.image = (annotation as? PhotoAnnotation)?.photo

UIGraphicsBeginImageContext(resizeRenderImageView.frame.size)
resizeRenderImageView.layer.renderInContext(UIGraphicsGetCurrentContext())
var thumbnail = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()
```

## Bonus 2: See Fullscreen Picture
![Image](http://i.imgur.com/mbfp9PL.gif)

- Tapping on an annotation's callout should push a view controller showing the
  full-size image.
- Add a button to the rightCalloutAccessoryView of type
  `UIButtonType.DetailDisclosure`
- Implement the [delegate
  method](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKMapViewDelegate_Protocol/index.html#//apple_ref/occ/intfm/MKMapViewDelegate/mapView:annotationView:calloutAccessoryControlTapped:)
  that gets called when a user taps on the accessory view to [launch the
  FullImageViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instm/UIViewController/performSegueWithIdentifier:sender:)
  using the segue identifier `fullImageSegue`.

## Bonus 3: Replace the Pin with an Image
![Image](http://i.imgur.com/WIwqNtn.gif)

- The annotation view should use a custom image to replace the default red pin.
- Set MKAnnotationView's image property to the same image in Bonus 2.
  `annotationView.image = thumbnail`
- You will need to resize your photo to thumbnail size.    
