---
layout: post
title:  "Parallax Scrolling in Unity"
date:   2014-05-16 12:00:00
categories: unity gamedev
---

One very common effect in 2D games is to simulate 3D worlds using [parallax scrolling](http://en.wikipedia.org/wiki/Parallax_scrolling parallax scrolling). This effect has been used for decades in a number of games, and like many things it is easy to implement in Unity.

Here you can see the wonderful effect in Puppy Warrior's TBD mining game project. (Please forgive the programmer art.)

`TODO: Image.`

#Theory#

The easiest to understand method is the "Layer Method", where you compose multiple images in a layer, but move them at different rates.

In the above image, notice that the clouds appear to be further away because they move much slower relative to the camera. You can see the same effect while driving down the road in most parts of the world, if you can see moutains in the distance. No matter how far you get, the moutnains appear to be in the same place.

The foreground on the other hand changes dramatically. The buildings move along with the player, and the background appears slower.

That is the essense of how to perform parallax scrolling, moving different layers (clouds, background, foreground) at different rates.

#Implementation in Unity#

I implemented this effect in Unity by dynamically adjusting the transform of the of the various objects. That is, as the camera moved the background objects -- that apeared far away -- move _with_ the camera. Since they move with the camera, they don't appear to move (since they are in the same position relative to where the camera is looking.)

Objects which are intended to appear closer to the camera don't move as quickly, and so they "get passed by".

{% highlight C# %}
using UnityEngine;
using System.Collections;

/// <summary>
/// Move the game object at a slower rate than the camera, giving a sense of depth.
/// http://en.wikipedia.org/wiki/Parallax_scrolling
/// 
/// The component works by storing the inital position of the camera and the
/// game object. If the game object never moved, it would appear to move at
/// same speed as the camera.
/// 
/// However, this behvaior instead moves the game object _with_ the camera so that
/// its movement appears to be slower relative to the camera.
/// 
/// A scale factor of 0 means that the object is stationary and doesn't appear to
/// move with the camera. (Objects in the foreground.)
/// 
/// A scale factor of 1 means that the object moves with the camera (and thus doesn't
/// appear to move at all). (Objects in the background, e.g. mountains.)
/// </summary>
public class ParallaxScroll : MonoBehaviour {

       public Camera cameraToTrack = null;

       public bool trackXAxis = true;
       public bool trackYAxis = true;

       /// <summary>
       /// How closely to follow the camera.
       /// 
       /// 0.0 - The object doesn't move from its starting position. It will appear
       ///       to be right in foreground. (Move away as the camera moves.)
       /// 1.0 - The object is in the same position relative to the camera. It will
       ///       appear to be in the background. (Like a distant mountain.)
       /// </summary>
       public float scaleFactor = 0.5f;

       private Vector3 startingPosition;
       private Vector3 cameraStartingPosition;

       void Start() {
         Assert.IsNotNull(cameraToTrack);

    startingPosition = this.transform.localPosition;
         cameraStartingPosition = cameraToTrack.transform.localPosition;
         }

         void Update() {
            Vector3 cameraDelta = cameraToTrack.transform.localPosition - cameraStartingPosition;

                Vector3 newPosition = startingPosition;  // Vector3 is a struct, makes a copy.
                  if (trackXAxis) {
                       newPosition.x += cameraDelta.x * scaleFactor;
                          }
                      if (trackYAxis) {
                             newPosition.y += cameraDelta.y * scaleFactor;
                                }
                              this.transform.localPosition = newPosition;
                              }
}
{% endhighlight %}

This is neat.