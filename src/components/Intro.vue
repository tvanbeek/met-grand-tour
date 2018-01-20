<template lang="html">
  <div>
    <section>
      <Parallax speedFactor=".55">
        <img src="../assets/lansdowne.jpg" />
      </Parallax>
    </section>
    <section>
      <b-container>
        <b-row>
          <b-col offset="2" cols="8" class="py-5">
            <h1>The Lansdowne Dining Room</h1>
            <p>The Metropolitan Museum of Art features 40 period rooms dating from about 50 BC to the end of the twentieth century. A period room is a special exhibition space in a museum in which various types of artworks are presented together surrounded by original wall, floor and/or ceiling finishes. Such rooms give you an impression of what life was like at a certain place and time in history.</p>
            <p>At the moment of writing, the British Galleries of The Met are closed for renovation. The British Galleries exhibit the museum’s collection of decorative arts and sculpture from Great Britain of the early 16th through 19th century, including no less than 3 period rooms.[<a href="#footnote-1" class="footnote-reference">1</a>] As they are currently not on view, this offers the outstanding opportunity to explore one of them, the Dining Room from Lansdowne House, via The Met’s digital platform.</p>
            <p id="footnote-1" class="footnote">1. <a href="http://www.metmuseum.org/press/news/2016/british-galleries">http://www.metmuseum.org/press/news/2016/british-galleries</a></p>
          </b-col>
        </b-row>
      </b-container>
    </section>
  </div>
</template>
<script>
import Parallax from 'vue-parallaxy';

export default {
  components: {
    Parallax,
  },
};
</script>

<style scoped lang="less">
@import "../style.less";

@title-font-size: 100px;
#title {
    position: relative;

    .gradient(rgba(255,255,0,0.5), rgba(0,255,255,0.7), -35);

    height: 100%;
    bottom: 0;
    width: 100%;
    position: absolute;
    text-align: center;

    // h1 {
    //     margin-top:  @title-font-size / 2;
    //     font-weight: lighter;
    //     text-shadow: 2px 2px 0 white;
    //     font-size: @title-font-size;
    // }
    // h2 {
    //     font-weight: bold;
    //     font-size: 60px;
    // }
}
</style>
