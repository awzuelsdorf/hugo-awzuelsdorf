---
title: "Geolocation Viewer and Cleaner for JPEG Images"
date: 2021-01-24T00:00:00-04:00
draft: true
---

***Overview***

In this article, I describe the features and future work for [a website](https://geolocation-viewer-and-cleaner.herokuapp.com/) that reads location data embedded in JPEG files' EXIF metadata to allow nontechnical users to view what information is in the images they are capturing and often uploading to websites or sending to others (such as the people they meet on dating sites). This can help people be aware of and maintain control over what information they offer to websites and others.

***Background***
			   
Many times, JPEG images taken on smartphones or even standalone cameras can contain more data than just the pixels of the image. An example of this is [this image](https://github.com/zhso/jpeg-exif/blob/master/test/IMG_0001.JPG), which appears to have been taken somewhere close to the Wangjing subdistrict of Beijing, People's Republic of China. Looking at just the data in this photo, one would not be able to tell where this photo was taken. But anyone who has this photo can look at its EXIF metadata to see the latitude/longitude of where it was taken, potentially posing a security risk to the whomever took the photo or anyone/anything captured in the photo.

***Viewer***

I've created [a spartan but functional website](https://geolocation-viewer-and-cleaner.herokuapp.com/) to allow users to view what EXIF metadata is in a JPEG photo they upload. None of this information is stored on the server and none of it is shared with third parties, making it an attractive tool for those who do not want to write or download their own geolocation software to parse their images.

For images that have metadata, there is also a link to the given latitude/longitude as displayed on Google Maps, so that one can see just how accurate this metadata can be (and how creepy it can be, too!)

There is a 10MB limit on file size, and rate limiting is in place so that one can only make 10 requests per minute per IP address. This isn't a completely robust way of limiting users, as one could spoof IP addresses (assuming one does not care about the response) or use Tor or a VPN to switch which IP address one is using. However, it should help block at least some bad actors from completely overwhelming the site with requests.

***Conclusion/Future Work***

I am still working on the cleaning part, which will allow the user to download the image with the relevant geolocation data erased. I also want to implement caching of the metadata based on the uploaded image data's hash so that anyone who repeatedly uploads the same image does not have to wait as long to get an answer back.

*Update (30 Aug. 2022): this website will go offline on 7 Sept. 2022 as a result of heroku changing its billing policies. While it may be rehosted elsewhere eventually, there are no plans to do so at this time due to limited interest in the site and the availability of other free, similarly reputable tools.*

