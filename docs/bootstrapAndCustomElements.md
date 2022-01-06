2022-01-06

# Bootstrap and customElements and shadowDOM

In 2021, I was upgrading an older AngularJs/Bootstrap app to ES6 customElements, not 3rd party JavaScript. Reasons why are covered in my customElements 2022-01-06 post.

Currently best practices for writing customElements recomend using shadowDom. I did not use shadowDom.  It just seemed silly to re-import all the bootstrap css all the time.
I know the browser can cache the requests, but it just seemed like extra work with no benefit.

I suppose I could have had top level components like the header use shadow dom, then upgrades could happen in isloation, but again just seemed like more work for a legacy app that 
does not change very often.

There were a few components where I used a local style element inside the custom element, I think it was for a HTMLDisplay element.  But I did not use shadowdom.

