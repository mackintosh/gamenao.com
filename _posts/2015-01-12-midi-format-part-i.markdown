---
layout: post
title:  "The MIDI format - Part I"
date:   2015-01-12 00:00:00
categories: midi, programming
---

As a side project I've been writing a library in Go to parse MIDI information. This series
of blog posts I'll show you how to write a program to read from a MIDI file.

Standard MIDI Format - SMF
---

The SMF file format contains header and track chunks which are composed of a series of bytes.
The **Header Chunk** describes the format of the MIDI file, the timing, and the number of tracks.
And the **Track Chunk** holds the event data for a particular track.

Will only cover the Header Chunk in this blog post. I'll continue in my 
next post about in more detail about the Track Chunk since its quite complex.

Header Chunk a.k.a "MThd Header"
---

The header follows a pretty simple format and easy to parse.
An important thing to note that the file format uses Big endian encoding. Here's an example:


{% highlight go %}
// header_chunk = "MThd" + <header_length> + <format> + <n> + <division>
type MThdHeader struct {
  Type      uint32
  Size      uint32
  Format    uint16
  NumTracks uint16
  PPQN      uint16
}
{% endhighlight %}

| **Type** | 4 byte string which is always "MThd"
| **Size** | Size of the header chunk
| **Format** | A value of 0 indicates the file only has 1 tracks. A value of 1 means two or more tracks, and a value of 2 indicates a combination of both
| **NumTracks** | Number of tracks in the midi file
| **PPQN** | Pulses per quarter note

<br>

Loading the file in Go
===

{% highlight go %}
func main() {
  header := MThdHeader{}

  file, err := os.Open("sample.mid")
  check(err)

  err = binary.Read(file, binary.BigEndian, &header)
  check(err)

  fmt.Println("NumTracks", header.NumTracks)
  fmt.Println("PPQN", header.PPQN)
  fmt.Println("Format", header.Format)
}
{% endhighlight %}

That's it for this post. In the next example I'll go into more detail
about how to read a Track.
