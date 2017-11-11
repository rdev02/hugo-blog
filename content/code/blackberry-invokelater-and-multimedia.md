---
title: BlackBerry invokeLater and Multimedia.
author: Viktor Halitsyn
date: 2009-09-23T09:37:00+00:00
type: post
categories:
  - tech
disqusIdentifier: 5152927578
tags:
  - java
  - mobile

---
Recently had an issue in the BlackBerry Java application: it was reported to crash if the sounds were on and played often. However it didn&#8217;t crash if sounds were played once in a while. After some investigation it turned out that there are no memory&nbsp;leaks&nbsp;but some thing blocked the event dispatch&nbsp;queue. I asked for help on&nbsp;[Stack Overflow][1]&nbsp;and&nbsp;[BlackBerry developer forums][2]&nbsp;but that didn&#8217;t lead me&nbsp;&nbsp;anywhere except that I thought that something definitely gets locked. as my sounds were played via 

<pre name="code">SoundPlayer sp = new SoundPlayer((String) ((SoundNotification) arg).getArg() + Constants.EXTENSION_MIDI);
        sp.setVolumeLevel(100);
        sp.setMaxPlaybackMilis(4000);
        invokeLater(sp);
</pre>

i though that maybe this is the concurrent resource access issue as some of the melodies had to be played more than one time. So I implemented a simple manager like

<pre name="code">class SoundManager implements Runnable {

  boolean _isRunning;
  Vector _playList;

  public SoundManager() {
    _isRunning = true;
    _playList = new Vector(10);
  }

  public synchronized void stop() {
    _isRunning = false;
  }

  public synchronized void addTrack(String item) {
    _playList.insertElementAt(item, _playList.size());
    notify();
  }

  public void run() {
    while (_isRunning) {
      if (_playList.isEmpty()) {
        synchronized (this) {
          try {
            wait();
          } catch (InterruptedException e) {
            // do nothing
          }
        }
        continue;
      } else {
        // no f..n queue in this SDK
        String item = null;
        synchronized (_playList) {
          item = (String) _playList.elementAt(0);
          _playList.removeElementAt(0);
        }
        final SoundPlayer sp = new SoundPlayer(item);
        while (sp.isPlaying())
          sleep(500);
        sp.setVolumeLevel(100);
        sp.setMaxPlaybackMilis(4000);
        sp.run();
      }
    }
  }

  private void sleep(long timeout) {
    try {
      Thread.sleep(timeout);
    } catch (InterruptedException e) {
    }
  }

}
</pre>

and launched it along with application. BTW I was pretty much&nbsp;surprised&nbsp;that there IS NO QUEUE in 4.0.2 blackberry JDK SO i had to use Vector(well din&#8217;t really have to but it was used all around the code so I just grabbed it to be able to asap). This solved the issue.
  
&nbsp;&nbsp;However the the root of the problem was different from what I thought(though the solution is still correct). The root of the problem was &nbsp;my misunderstanding of the invokeLater method. It is misleading that it takes Runnable as a param &#8211; it actually executes your runnable on the event dispatch queue! Gotcha! Some of my sounds were 13 seconds long &#8211; no wonder firmware blocked the app with &nbsp;&#8220;<span style="border-collapse: collapse; font-family: Arial, Helvetica, sans-serif; font-size: 14px; line-height: 18px;">Application is not responding</span>&#8221; exception.

 [1]: http://stackoverflow.com/questions/1459681/application-crashes-with-exception-when-using-javax-microedition-media-player/1464802#1464802
 [2]: http://supportforums.blackberry.com/rim/board/message?board.id=java_dev&message.id=61681&jump=true#M61681