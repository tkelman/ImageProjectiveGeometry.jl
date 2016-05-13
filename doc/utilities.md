Utilities Function Reference
============================

## Index

* [nonmaxsuppts](#nonmaxsuppts) - Non-maximal suppression for features/corners.
* [derivative3](#derivative3) - 3-Tap discrete derivative filters.
* [derivative5](#derivative5) - 5-Tap 1st and 2nd discrete derivatives.
* [derivative7](#derivative7) - 7-Tap 1st and 2nd discrete derivatives.
* [gaussfilt](#gaussfilt) -  Small wrapper function for convenient Gaussian filtering.
* [dilate1d](#dilate1d) - 1D morphological dilation of a signal
* [erode1d](#erode1d) - 1D morphological erosion of a signal
* [imdilate](#imdilate) - Image morpholgical dilation.
* [imerode](#imerode) - Image morpholgical erosion.
* [circularstruct](#circularstruct) - Generate circular structuring element for morphological operations.
* [floatyx](#floatyx) - Convert 2D AbstractImage to 2D float array with y x spatial order.

* [histtruncate](#histtruncate) - Truncates ends of an image histogram.
* [normalise/normalize](#normalisenormalize) - Normalises image values to 0-1, or to desired mean and variance.
* [matchbycorrelation](#matchbycorrelation) - Match image feature points by correlation.
_________________


## nonmaxsuppts - Non-maximal suppression for features/corners.

Non maxima suppression and thresholding for points generated by a feature
or corner detector.

```
Usage:   (r,c) = nonmaxsuppts(cimg; radius=1, thresh=0, N=Inf, subpix=false, img=[], fig=1)
                                                            
Argument:
           cimg   - corner strength image.

Keyword Arguments:
           radius - radius of region considered in non-maximal
                    suppression. Typical values to use might
                    be 1-3 pixels. Default is 1.
           thresh - Threshold, only features with value greater than
                    threshold are returned. Default is 0.
                N - Maximum number of features to return.  In this case the
                    N strongest features with value above 'thresh' are
                    returned. Default is Inf.
         subpixel - If set to true features are localised to subpixel
                    precision. Default is false.
             img  - Optional image data.  If an image is supplied the
                    detected corners are overlayed on this image. This can
                    be useful for parameter tuning. Default is [].
              fig - Optional figure number to display image and corner points. 

Returns:
           r,c    - row and column coordinates of corner points.
```

Example of use:

```
 > hes = hessianfeatures(img, 1)   # Compute Hessian feature image in image img
```

Find the 1000 strongest features to subpixel precision using a non-maximal
suppression radius of 2 and overlay the detected corners on the origin image.

```
 > (r,c) = nonmaxsuppts(abs(hes), radius=2, N=1000, img=img, subpixel=true)
```

Note: An issue with integer valued images is that if there are multiple pixels
all with the same value within distance 2*radius of each other then they will
all be marked as local maxima. 

See also: harris(), noble(), shi_tomasi(), hessianfeatures()


## derivative3 - 3-Tap discrete derivative filters.

This function computes 1st derivatives of an image using the 3-tap
coefficients given by Farid and Simoncelli.  The results are significantly
more accurate than simple gradient functions on edges that are at angles
other than vertical or horizontal. This in turn improves gradient orientation
estimation enormously.  If you are after extreme accuracy try using derivative7().

```
Usage:  (gx, gy) = derivative3(img, derivative specifiers)

Arguments:
                     img - Image to compute derivatives from. ::Array{T,2}
   derivative specifiers - A tuple of character strings
                           that can be any of "x" or "y"
                           These can be in any order, the order of the
                           computed output arguments will match the order
                           of the derivative specifier strings.
Returns:
 Function returns requested derivatives which can be:
    gx, gy   - 1st derivative in x and y

 Example:
   Compute 1st derivatives in x and y
   (gx, gy) = derivative3(img, ("x", "y"))
```                                           

See also: derivative5(), derivative7()


## derivative5 - 5-Tap 1st and 2nd discrete derivatives.

This function computes 1st and 2nd derivatives of an image using the 5-tap
coefficients given by Farid and Simoncelli.  The results are significantly
more accurate than simple gradient functions on edges that are at angles
other than vertical or horizontal. This in turn improves gradient orientation
estimation enormously.  If you are after extreme accuracy try using derivative7().

```
Usage:  (gx, gy, gxx, gyy, gxy) = derivative5(img, derivative_specifiers)

Arguments:
                     img - Image to compute derivatives from. ::Array{T,2}
   derivative specifiers - A tuple of character strings
                           that can be any of "x", "y", "xx", "yy" or "xy"
                           These can be in any order, the order of the
                           computed output arguments will match the order
                           of the derivative specifier strings.
Returns:
 Function returns requested derivatives which can be:
    gx, gy   - 1st derivative in x and y
    gxx, gyy - 2nd derivative in x and y
    gxy      - 1st derivative in y of 1st derivative in x

 Examples:
   Just compute 1st derivatives in x and y
   (gx, gy) = derivative5(img, ("x", "y"))
                                           
   Compute 2nd derivative in x, 1st derivative in y and 2nd derivative in y
   (gxx, gy, gyy) = derivative5(img, ("xx", "y", "yy"))
```

See also: derivative7()


## derivative7 - 7-Tap 1st and 2nd discrete derivatives.

This function computes 1st and 2nd derivatives of an image using the 7-tap
coefficients given by Farid and Simoncelli.  The results are significantly
more accurate than simple gradient functions on edges that are at angles
other than vertical or horizontal. This in turn improves gradient orientation
estimation enormously.

```
Usage:  (gx, gy, gxx, gyy, gxy) = derivative7(img, derivative specifiers)

Arguments:
                      im - Image to compute derivatives from. ::Array{T,2}
   derivative specifiers - A tuple of character strings
                           that can be any of "x", "y", "xx", "yy" or "xy"
                           These can be in any order, the order of the
                           computed output arguments will match the order
                           of the derivative specifier strings.
Returns:
 Function returns requested derivatives which can be:
    gx, gy   - 1st derivative in x and y
    gxx, gyy - 2nd derivative in x and y
    gxy      - 1st derivative in y of 1st derivative in x

 Examples:
   Just compute 1st derivatives in x and y
   (gx, gy) = derivative7(img, ("x", "y"))  
                                           
   Compute 2nd derivative in x, 1st derivative in y and 2nd derivative in y
   (gxx, gy, gyy) = derivative7(img, ("xx", "y", "yy"))
```

See also: derivative5()


## gaussfilt -  Small wrapper function for convenient Gaussian filtering.

```
Usage:  smimg = gaussfilt(img, sigma)

Arguments:  img - Image to be smoothed, can be multi-channel.
          sigma - Standard deviation of Gaussian filter.

Returns:  smimg - Smoothed image.
```

The function automatically generates a filter with size being the
first odd integer >= 6*sigma.

If called with sigma = 0 the function immediately returns with im assigned
to smim

See also:  integgaussfilt()



## dilate1d - 1D morphological dilation of a signal.

```
Usage:  df = dilate1d(f, k)

Arguments:  f - 1D array of values to be dilated
            k - Size of dilation kernel 

Returns:   df - Array of dilated values
```

Note that if the size of the structuring element is even then the centre pixel
is taken to be the integer pixel location to the right of the ideal.

This function uses Marcel van Herk's algorithm to run in linear time with
respect to the length of the signal, irrespective of the structing element
size.

See also: erode1d(), imdilate(), imerode()


## erode1d - 1D morphological erosion of a signal.

```
Usage:  ef = erode1d(f, k)

Arguments:  f - 1D array of values to be eroded
            k - Size of erosion kernel 

Returns:   ef - Array of eroded values
```

Note that if the size of the structuring element is even then the centre pixel
is taken to be the integer pixel location to the right of the ideal.

This function uses Marcel van Herk's algorithm to run in linear time with
respect to the length of the signal, irrespective of the structing element
size.

See also: dilate1d(), imerode(), imdilate()


## imdilate - 2D morphological dilation with rectangular or octagonal structing element

```
Usage: dimg = imdilate(img, seType, seSize)

Arguments: img - Image to be dilated
        seType - String either "rectangle" or "octagon" specifying the
                 structuring element type. Can be shortened to "rect" or
                 "oct".
        seSize - Structuring element size.  
                 If the seType is 'rect' seSize can be a 2 element vector
                 indicating the size of the rectangular structuring element
                 [rows, cols], or if it is a single value the structuring
                 element is square [seSize x seSize]
                 If seType is 'oct' then seSize is the nominal radius of
                 the octagonal structuring element. 

Returns:  dimg - The dilated image.
```

Note that the radius of the octagonal structuring element is somewhat nominal
due to discrete approximations.  If anything the structuring element may end
up with a slightly larger radius than specified rather than being smaller.

This function uses Marcel van Herk's algorithm to run in linear time with
respect to the size of the image, irrespective of the structing element size.

See also imerode(), erode1d(), dilate1d()


## imdilate - 2D morpholgical dilation with arbitrary structuring element

```
Usage:   dimg = imdilate(img, se)

Arguments:
          img - Image to be dilated.  May be greyscale or binary
                ::Array{T,2}
           se - Structuring element defined by a 2D Bool array

Returns:
         dimg - dilated image
```

See also: imerode(), circularstruct()


imerode - 2D morphological erosion with rectangular or octagonal structing element

```
Usage: eimg = imerode(img, seType, seSize)

Arguments: img - Image to be eroded
        seType - String either "rectangle" or "octagon" specifying the
                 structuring element type. Can be shortened to "rect" or
                 "oct".
        seSize - Structuring element size.  
                 If the seType is 'rect' seSize can be a 2 element vector
                 indicating the size of the rectangular structuring element
                 [rows, cols], or if it is a single value the structuring
                 element is square [seSize x seSize]
                 If seType is 'oct' then seSize is the nominal radius of
                 the octagonal structuring element. 

Returns:  eimg - The eroded image.
```

Note that the radius of the octagonal structuring element is somewhat nominal
due to discrete approximations.  If anything the structuring element may end
up with a slightly larger radius than specified rather than being smaller.

This function uses Marcel van Herk's algorithm to run in linear time with
respect to the size of the image, irrespective of the structing element size.

See also imdilate(), erode1d(), dilate1d()


## imerode - 2D morpholgical erosion with arbitrary structuring element

```
Usage:   dimg = imerode(img, se)

Arguments:
          img - Image to be eroded.  May be greyscale or binary
                ::Array{T,2}
           se - Structuring element defined by a 2D array

Returns:
         dimg - eroded image
```

See also: imdilate(), circularstruct()

## circularstruct - Generate circular structuring element for morphological operations

```
Usage:  se = circularstruct(radius)

Argument:  radius - Desired radius of structuring element
Returns:       se - Bool structuring element of size (2*radius+1)^2

```

See also: imdilate(), imerode()

## floatyx - Convert 2D AbstractImage to float data in y x spatial order

```
Usage:  (fimg, prop) = floatyx(img)

Argument:  img - ::AbstractImage{T,2}

Returns:  fimg - ::Array{Float64,2} in "y" "x" spatial order.
          prop - A copy of the properties dictionary of the input image 
                 with "spatialorder" adjusted (if this was needed).
```

Most image processing functions expect 2D arrays in (row, column)
format and most feature localisation functions return corner and edge
coordinates in terms of (row, column) coordinates.

This convenience function takes a 2D AbstractImage, extracts the data,
converts it to a 2D Float64 array and, if necessary, transposes the data
so that it is in "y" "x" (row, column) spatial order.  The
AbstractImage spatial order property is set to ["y","x"] and the
properties returned.


## histtruncate

Truncates ends of an image histogram.

Function truncates a specified percentage of the lower and
upper ends of an image histogram.

This operation allows grey levels to be distributed across
the primary part of the histogram.  This solves the problem
when one has, say, a few very bright values in the image which
have the overall effect of darkening the rest of the image after
rescaling.

```
Usage:
1)   newimg = histtruncate(img, lHistCut, uHistCut)
2)   newimg = histtruncate(img, HistCut)

Arguments:
 Usage 1)
   img         -  Image to be processed.
   lHistCut    -  Percentage of the lower end of the histogram
                  to saturate.
   uHistCut    -  Percentage of the upper end of the histogram
                  to saturate.  If omitted or empty defaults to the value
                  for lHistCut.
 Usage 2)
   HistCut     -  Percentage of upper and lower ends of the histogram to cut.

Returns:
   newimg      -  Image with values clipped at the specified histogram
                  fraction values.  If the input image was colour the
                  lightness values are clipped and stretched to the range
                  0-1.  If the input image is greyscale no stretching is
                  applied. You may want to use normalise() to achieve this.
```

## normalise/normalize

Normalises image values to 0-1, or to desired mean and variance.

```
Usage 1:      nimg = normalise(img)

```
Offsets and rescales image so that the minimum value is 0
and the maximum value is 1.  

```
Usage 2:      nimg = normalise(img, reqmean, reqvar)

Arguments:  img     - A grey-level input image.
            reqmean - The required mean value of the image.
            reqvar  - The required variance of the image.
```
Offsets and rescales image so that nimg has mean reqmean and variance
reqvar.  

## matchbycorrelation - Match image feature points by correlation.

Function generates putative matches between previously detected
feature points in two images by looking for points that are maximally
correlated with each other within windows surrounding each point.
Only points that correlate most strongly with each other in both
directions are returned.

This implements normalised cross correlation in its most basic form.
There is no attempt to deal with scale or orientation differences
between the images and thus it will only work if the images are not
too dissimilar.

This is a simple-minded N^2 comparison.

```
Usage: (m1, m2, p1ind, p2ind, cormat) = 
                matchbycorrelation(img1, p1, img2, p2, w, dmax)

Arguments:
      img1, img2 - Images containing points that we wish to match.
        p1, p2   - Coordinates of feature pointed detected in img1 and
                   img2 respectively using a corner detector (say Harris
                   or phasecong3).  p1 and p2 are [2 x npts] arrays though
                   p1 and p2 are not expected to have the same number
                   of points.  The first row of p1 and p2 gives the row
                   coordinate of each feature point, the second row
                   gives the column of each point.
        w        - Window size (in pixels) over which the correlation
                   around each feature point is performed.  This should
                   be an odd number.
        dmax     - (Optional) Maximum search radius for matching
                   points.  Used to improve speed when there is little
                   disparity between images. Even setting it to a generous
                   value of 1/4 of the image size gives a useful
                   speedup. If this parameter is omitted it defaults to Inf. 

Returns:
        m1, m2   - Coordinates of points selected from p1 and p2
                   respectively such that (putatively) m1[:,i] matches
                   m2[:,i]. m1 and m2 are [2 x npts] arrays defining the
                   points in each of the images in the form [row;col].
  p1ind, p2ind   - Indices of points in p1 and p2 that form a match.  Thus,
                   m1 = p1[:,p1ind] and m2 = p2[:,p2ind]
        cormat   - Correlation matrix; rows correspond to points in p1,
                   columns correspond to points in p2
```

This function is slow as mud! Needs some attention.
