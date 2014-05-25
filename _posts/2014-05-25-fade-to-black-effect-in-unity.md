---
layout: post
title:  "A fade-to-black effect for Unity"
date:   2014-05-25 12:00:00
categories: unity
---

The ability to fade the scene to black can be a very useful effect in a game. It makes a great transition between scenes.

In _Space Miner_ the screen fades to black after the player dies before the Game Over UI appears.

![Fade to Black](/images/fade-to-black-example.gif "Fade to Black example")


If you look around you can find several ways to achieve this [effect](http://unity3d.com/learn/tutorials/projects/stealth/screen-fader). The easiest approach is to put a texture in front of the screen, and then fading it to black.

You can see it in the following code snippet. Note that it uses a `GUITexture` so it doesn't need to interact with the current camera.

    using System.Collections;
    using UnityEngine;

    [RequireComponent(typeof(GUITexture))]
    public class FadeToBlackService : MonoBehavior {

        /// <summary>
        /// Time, in seconds, to complete the fade.
        /// </summary>
        public float fadeSpeed = 3.0f;

        // Should the screen fade to black or clear?
        private bool fadingToBlack = false;

        // Handle to the GUITexture rendered in front of the screen.    
        private GUITexture theGuiTexture = null;

        // Time the fade started.
        private float fadeStartTime;

        void Start() {
            theGuiTexture = GetComponent<GUITexture>();
            if (theGuiTexture == null) {
                throw new UnityException("GUITexture must be attached.");
            }
            if (theGuiTexture.texture == null) {
                throw new UnityException("A texture must be set.)");
            }

            // Set the texture so that it covers the screen.
            theGuiTexture.pixelInset = new Rect(
                0.0f, 0.0f, Screen.width, Screen.height);
            theGuiTexture.color = Color.clear;

            // Start with the fade complete.
            fadeStartTime = -fadeSpeed;
        }

        void Update() {
            float t = (Time.time - fadeStartTime) / fadeSpeed;
            t = Mathf.Clamp01(t);

            Color startColor = fadingToBlack ? Color.clear : Color.black;
            Color finishColor = fadingToBlack ? Color.black : Color.clear;
            theGuiTexture.color = Color.Lerp(startColor, finishColor, t);
        }

        public void FadeToBlack() {
            fadeStartTime = Time.time;
            fadingToBlack = true;
        }

        public void FadeToClear() {
            fadeStartTime = Time.time;
            fadingToBlack = false;
        }
    }

To start the effect, simply call `FadeToBlack`.