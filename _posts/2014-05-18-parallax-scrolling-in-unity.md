---
layout: post
title:  "Parallax Scrolling in Unity"
date:   2014-05-18 12:00:00
categories: unity gamedev
---

One very common effect in 2D games is to simulate 3D worlds using [parallax scrolling](http://en.wikipedia.org/wiki/Parallax_scrolling "parallax scrolling"). This effect has been used for decades in a number of games, and fortunately it is also easy to implement in Unity.

Here you can see the wonderful effect put to good use in Puppy Warrior's first project _Space Miner_.

![Space Miner](/images/parallax-scrolling-example.gif "Space Miner example")

#Theory#

In the previous image, notice how the clouds, debris, and buildings all move at different rates. And thus, the clouds seem to be far away with the buildings being much closer. That is the essense of how to perform parallax scrolling.

#Implementation in Unity#

I implemented this effect in Unity by dynamically adjusting the transform of the of the various layers. As the camera moved, the background objects have their position adjusted to move _with_ the camera.

Objects which are intended to appear closer to the camera don't move as quickly, and so they "get passed by". Objects that are intended to be far in the distance move with the camera, so they appear to be almost stationary.

Here's the code:

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

    Vector3 newPosition = startingPosition;
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

To apply the effect then, simply add the `ParallaxScroll` Component to the root object of each layer you wish to move.
