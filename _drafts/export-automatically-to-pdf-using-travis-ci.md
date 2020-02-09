---
layout: post
title: "[reveal.js] Export automatically your slideshow to PDF using Travis CI"
categories:
    - Development
    - SysAdmin
tags:
    - reveal.js
    - Slideshow
---
For those who don't already know it, [reveal.js][reveal.js] is "a framework for easily creating beautiful presentations using HTML". I suggest you to have a look at the [official live demo][reveal.js live demo] or at my slideshows listed [on this page][my publications] to get a more precise idea of what this framework can offer in terms of features / rendering.

Even if it is great to be able to open these slideshows on any device having a decent web browser, providing a PDF version is always a plus. The [reveal.js documentation][reveal.js pdf export] suggests to export the presentation to PDF using the **Save as PDF** feature of Chromium / Google Chrome or to use the [DeckTape][decktape] project. In this post, I would like to show a way to automate the PDF exporting process using a typical software development pipeline.

Considering a public presentation hosted on [GitHub Pages][github pages], [Travis CI][travis ci] would perfectly fit what needed to carry out this task. It is a free, easy-to-use and open-source hosted continuous integration and deployment platform. If you haven't used it in the past, I invite you to follow the [Travis CI setup guide][travis setup].

Now, let's describe a bit more how the whole development pipeline will look like. The reveal.js presentation's source code will be versioned with Git, hosted on GitHub Pages and Travis CI will be the continuous integration and deployment system. On every new release (new git tag), Travis CI will be asked to generate the PDF version of the slideshow and to upload it as an attachment to the corresponding release note on GitHub. DeckTape will be used to generate the PDF as it can perform this task with one command line. Furthermore, Travis CI will be used to check the validity of the presentation's HTML code in addition of checking all the external HTTP links present in the document. [HTMLProofer][html-proofer] will be used for this part.

![Diagram of the export-to-pdf pipeline][export to pdf pipeline diagram]

To use DeckTape with Travis CI,

    sudo: required

    services:
      - docker

    before_deploy: docker run --rm -u $(id -u):$(id -g) -v $(pwd):/home astefanutti/decktape /home/index.html /home/slideshow.pdf

HTMLProofer...

    language: ruby

    env:
      global:
        - NOKOGIRI_USE_SYSTEM_LIBRARIES=true

    addons:
      apt:
        packages:
          - libcurl4-openssl-dev

    install: gem install html-proofer

    script: htmlproofer index.html

To upload the resulting PDF to the corresponding release note on GitHub...

    deploy:
      provider: releases
      api_key:
        secure: "Fl7Ai5kroLiB1ERtOSmyMSOwavmrPsZ6IDwRheqmLnaG42xVrlBRoQdwRm5MM4UmTNxYKz4P9m/mjyfErsvPo85Mt+++hM8KJGYaDQCiOxuaT79/JuqfKhCG5LCbdI6iPhtb4zU7VEN2odN2RXe0VkbLwX1Z7rHg8AEAGeYhfCmRFfh0w1ZN3yHpLY5FVLxGSuGOTMBA6iRq/iGAxUOTs/um4yVzK3rO/16lu8iyW0dn9yl8h9ous67MmIlx09SuTCkCtHIM97uvGH2WlDYpv+eaosxawlPwk29LIZBX6MbgV2ivMlYhC4NvTrwcf3XAqei8FRKzLEd6HcLdhQKms3qPpGCI8tYg9K/axG59NMWn9NgwZe/VMU1+DXHc3RVKyDbkbOlZeiqfIDZos38RTBMLe+/VinOIs6YLegBBlqfrwqXzzDFxqSsrgYIgXJcV7Kvm1s1Kej3YYz0IFIduolFwmtBa312uLBq4DQz9ReVo0Me7AZMQkIiP/x44AlWw9yIsf9Cqc8rOxeyku697Cd6X9iID4o3yWXtWasEFgQkBvBVQO0MMH04Z2BH8WenJQlw62POkNVXtXGq7qNpeDrYMYCvicpv7TmmOLAADqQGDYaLLz/mNqYlqf/fTQkaLOwHGN0k0q5sLn1s71HsYrQnLlI4Md6mtZEFjeX9qxO4="
      file: slideshow.pdf
      skip_cleanup: true
      overwrite: true
      on:
        repo: SkypLabs/bsfl-slideshow
        tags: true

 [decktape]: https://github.com/astefanutti/decktape
 [export to pdf pipeline diagram]: /images/reveal-js-export-to-pdf.svg
 [github pages]: https://pages.github.com/
 [html-proofer]: https://github.com/gjtorikian/html-proofer
 [my publications]: /publications/
 [reveal.js]: https://github.com/hakimel/reveal.js
 [reveal.js live demo]: https://revealjs.com/
 [reveal.js pdf export]: https://github.com/hakimel/reveal.js#pdf-export
 [travis ci]: https://travis-ci.org/
 [travis setup]: https://docs.travis-ci.com/user/getting-started/
