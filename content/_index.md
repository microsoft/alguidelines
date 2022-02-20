---
title: "ALGuidelines.dev"
linkTitle: "ALGuidelines.dev"
description: "Design Patterns and Guidelines for AL development for Microsoft Dynamics 365 Business Central"
images: ["images/og-image-fission.png"]
---

<div class="hero">
  <div class="container">
    <div class="row justify-content-center align-items-center">
      <div class="col-xl-7 col-lg-6 col-md-6 hero-section-mobile">
        <h1 class="hero-title">
          Guidelines and Patterns<br />
          for AL for<br />
          Microsoft Dynamics 365 Business Central
        </h1>
        <img
          class="img-fluid hero-banner show-mobile-big"
          src="/images/fission-illustration.svg"
          alt-text="Fission Banner"
        />
        <br />
        <a href="/docs/installation/"
          ><button class="hero-big">
            <img
              src="/images/github-icon.png"
              width="22px"
              style="margin-right: 12px"
            />Install Fission
          </button></a
        >
      </div>

  <div class="col-xl-5 col-lg-6 col-md-6 show-desktop-big">
        <img
          class="img-fluid hero-banner"
          src="/images/fission-illustration.svg"
          alt-text="Fission Banner"
        />
      </div>
    </div>
  </div>
</div>

<div class="body-container">
  <div class="container">
    <br />
    <div class="row">
      <div class="col-lg-5">
        <h2 class="section-title">What is Fission?</h2>
        <p class="section-text-bold">
          Fission is a framework for serverless functions on Kubernetes.
        </p>
        <p class="section-text">
          Write short-lived functions in any language, and map them to HTTP
          requests (or other event triggers).
        </p>
        <p class="section-text">
          Deploy functions instantly with one command. There are no containers
          to build, and no Docker registries to manage.
        </p>
        <a href="/docs/installation/"
          ><button class="hero-mid">Learn More &amp; Install Fission</button></a
        >
      </div>

  <div class="col-lg-7">
        <img
          class="img-fluid show-mobile-margin"
          src="/images/body-img-1.svg"
          alt-text="Fission Banner"
        />
      </div>
    </div>
  </div>
</div>

<div class="wide-image show-desktop">
  <img src="/images/wave.webp" class="big-waves" />
</div>

<div class="body-container push-up">
  <div class="container">
    <div class="row">
      <div class="col-lg-12 center-text" style="margin-bottom: 50px">
        <h2 class="section-title">Benefits</h2>
      </div>

  <div class="col-lg-4">
        <div class="card-shadow">
          <img src="/images/focus.svg" width="50px" class="img-card" />
          <h4 class="section-text-bold">Focus on Code,<br />Not Plumbing</h4>
          <p class="section-text">
            Fission lets developers run code functions easily, while automating
            the menial work of configuring Kubernetes micro-services concepts
            behind the scenes. Just write your code functions and Fission will
            make it run on Kubernetes.
          </p>
        </div>
      </div>
      <div class="col-lg-4">
        <div class="card-shadow">
          <img src="/images/portable.svg" width="50px" class="img-card" />
          <h4 class="section-text-bold">
            Portable: Run your Lambda-like service, anywhere
          </h4>
          <p class="section-text">
            Fission is open source under the Apache license. Fission works on a
            Kubernetes cluster anywhere: on your laptop, in any public cloud, or
            in a private data-center. Write your application with the
            flexibility of deploying it anywhere.
          </p>
        </div>
      </div>
      <div class="col-lg-4">
        <div class="card-shadow">
          <img src="/images/extensible.svg" width="50px" class="img-card" />
          <h4 class="section-text-bold">
            Extensible and<br />
            Customizable
          </h4>
          <p class="section-text">
            Fission is extensible to any programming language (Python, NodeJS,
            Go, C#, PHP are supported today). It abstracts away containers by
            default, but you can build your own containers if you need to.
          </p>
        </div>
      </div>
    </div>
  </div>

  <div class="container">
    <div class="row feature-section">
      <div class="col-lg-12 center-text" style="margin-bottom: 50px">
        <h2 class="section-title">Features</h2>
      </div>
      <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img
              src="/images/kubernetes-native.svg"
              width="36px"
              class="img-card-header"
            />
            <h4 class="section-text-bold">Kubernetes Native</h4>
          </div>
          <p class="section-text">
            Fission is open source under the Apache License. It works on a
            Kubernetes cluster anywhere: in your private datacenter, in a public
            cloud, or on your laptop.
          </p>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img
              src="/images/fast-cold.svg"
              width="36px"
              class="img-card-header"
            />
            <h4 class="section-text-bold">Fast Cold-Start</h4>
          </div>
          <p class="section-text">
            Fission maintains a configurable pool of containers, so your
            functions have very low cold-start latencies, typically ~100msec.
          </p>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img
              src="/images/declarative.svg"
              width="36px"
              class="img-card-header"
            />
            <h4 class="section-text-bold">
              Declarative Application Specifications
            </h4>
          </div>
          <p class="section-text">
            Declarative specifications give the developer the capability to
            define a function once and then deploy it anywhere. Fission
            resources (functions, triggers etc.) can be checked into source
            control, and the application can be recreated from them.
          </p>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img
              src="/images/administration.svg"
              width="36px"
              class="img-card-header"
            />
            <h4 class="section-text-bold">
              Administration & Operational Simplicity
            </h4>
          </div>
          <p class="section-text">
            Fission aims for operational simplicity by incorporating logs
            directly into the CLI, via integration with Fluentd. It also
            integrates with Prometheus for metrics tracking and dashboard
            visibility.
          </p>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img src="/images/istio.svg" width="36px" class="img-card-header" />
            <h4 class="section-text-bold">Istio Service-mesh Integration</h4>
          </div>
          <p class="section-text">
            Fission integrates with Istio, which is an open platform to connect,
            manage and secure micro-services. With Istio, users also get the
            ability to monitor function usage and trace request latency through
            dashboards.
          </p>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img
              src="/images/language.svg"
              width="36px"
              class="img-card-header"
            />
            <h4 class="section-text-bold">Wide Language Support</h4>
          </div>
          <p class="section-text">
            Fission is extensible to any programming language of your choice.
            Python, NodeJS, Go, C#, PHP are supported today, but you can build
            your own custom containers if you need to.
          </p>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="feature-card">
          <div class="card-header-custom">
            <img
              src="/images/autoscaling.svg"
              width="36px"
              class="img-card-header"
            />
            <h4 class="section-text-bold">Function Autoscaling</h4>
          </div>
          <p class="section-text">
            Fission provides autoscaling for functions based on CPU usage. In
            the future, custom metrics will be also supported for scaling
            functions. You can set the initial and maximum CPU for a function
            and target CPU at which autoscaling will be triggered.
          </p>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="wide-image show-desktop">
  <img src="/images/wave.webp" class="big-waves" />
</div>

<div class="body-container push-up">
  <div class="container">
    <div class="row">
      <div class="col-lg-12 center-text" style="margin-bottom: 50px">
        <h2 class="section-title">Use Cases</h2>
      </div>
      <div class="col-lg-10 center-text info-card" style="margin-bottom: 50px">
        <h3 class="section-text-bold">Build Serverless API Backends</h3>
        <p class="section-text">
          Create API backends for web and mobile apps without managing servers.
          Just write functions, and map them to HTTP routes. Fission takes care
          of the rest: deployment, routing, scalability, availability. Use
          Kubernetes service discovery and networking to interoperate with
          other services, like Redis, Postgres, Etcd etc.
        </p>
        <img
          src="/images/use-case-1.png"
          class="img-fluid"
          alt-text="Build Serverless API Backends use case diagram"
        />
        <br /><br />
      </div>

  <div class="col-lg-10 center-text info-card" style="margin-bottom: 50px">
        <h3 class="section-text-bold">Easily Implement Webhooks</h3>
        <p class="section-text">
          Webhooks are a popular way to integrate with third-party services.
          Slack provides webhooks that are triggered by certain words or
          messages; GitHub provides webhooks triggered by events in Git
          repositories. Fission is a great place to implement webhooks: just
          write the code, map it to a URL, and point the webhook at that URL.
        </p>
        <img
          src="/images/use-case-2.png"
          class="img-fluid"
          alt-text="Easily implement webhook use case diagram"
        />
        <br /><br />
      </div>

  <div class="col-lg-10 center-text info-card" style="margin-bottom: 50px">
        <h3 class="section-text-bold">Write Kubernetes Event Handlers</h3>
        <p class="section-text">
          By subscribing to Kubernetes watches, you can write custom automation
          for your Kubernetes infrastructure. Fission’s integration with
          Kubernetes watches allows you to monitor resources such as Pods and
          Services, and execute arbitrary functions when the watched set of
          resources change.
        </p>
        <img
          src="/images/use-case-3.png"
          class="img-fluid"
          alt-text="Write Kubernetes Event Handlers use case diagram"
        />
        <br /><br />
      </div>
    </div>
  </div>
</div>

<div class="body-container whats-new">
  <div class="container">
    <div class="row">
      <div class="col-lg-12 center-text" style="margin-bottom: 40px">
        <h2 class="section-title">What's New</h2>
      </div>
      <div class="col-lg-4">
        <div class="card-shadow">
          <span class="card-badge">FISSION DZONE REFCARD</span>
          <h4 class="section-text-bold mt-4">
            New! Download the <span class="d-lg-block">Fission.io Refcard</span>
          </h4>
          <div class="card-shadow-content">
            <p class="section-text">
              Get started with Serverless on Kubernetes in 5 minutes! Learn how
              to set up Fission, its key concepts, how to create and invoke a
              function, monitor its performance, and more.
            </p>
            <a
              href="https://platform9.com/wp-content/uploads/2019/03/dzone-refcard-fissionio.pdf"
              ><button class="hero-mid align-self-end">Download Now!</button></a
            >
          </div>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="card-shadow">
          <span class="card-badge">BLOG</span>
          <h4 class="section-text-bold mt-4">
            Fission PostgreSQL Database <span class="d-lg-block">Sample</span>
          </h4>
          <div class="card-shadow-content">
            <p class="section-text">
              Learn how to use Fission functions with PostgreSQL database and
              perform basic operations on it.
            </p>
            <a href="/blog/how-to-use-postgresql-database-with-fission-functions/"
              ><button class="hero-mid-2 align-self-end">Read More</button></a
            >
          </div>
        </div>
      </div>

  <div class="col-lg-4">
        <div class="card-shadow">
          <span class="card-badge">BLOG</span>
          <h4 class="section-text-bold mt-4">
            Next.js Application with <span class="d-lg-block">Fission</span>
          </h4>
          <div class="card-shadow-content">
            <p class="section-text">
             Learn how you can create a Next.js application with Fission.
             We create a Next.js blog in this tutorial.
            </p>
            <a
              href="/blog/serverless-next.js-example-blog-with-fission/"
              ><button class="hero-mid-2 align-self-end">Read More</button></a
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="body-container">
  <div class="container">
    <div class="row">
      <div class="col-lg-4">
        <br />
        <br />
        <h1 class="section-title">Join the Community</h1>
        <br />

  <div class="show-mobile">
          <div class="community-cards">
            <a href="/slack"
              ><div class="card-shadow">
                <div class="card-header-custom">
                  <img
                    src="/images/slack.svg"
                    width="30px"
                    class="img-card-header"
                  />
                  <p class="section-text-bold">Slack</p>
                </div>
                <p class="section-text">
                  Join the Fission community to engage with the fission team,
                  interact with other users, and contribute ideas and feedback.
                </p>
              </div></a
            >

  <a href="https://github.com/fission"
              ><div class="card-shadow">
                <div class="card-header-custom">
                  <img
                    src="/images/github.svg"
                    width="30px"
                    class="img-card-header"
                  />
                  <p class="section-text-bold">GitHub</p>
                </div>
                <p class="section-text">
                  Dive into the code and learn more about Fission. Submit PRs
                  and collaborate with other like-minded contributors.
                </p>
              </div></a
            >
            <a href="https://twitter.com/fissionio"
              ><div class="card-shadow twitter-card" style="margin-left: auto">
                <div class="card-header-custom">
                  <img
                    src="/images/twitter.svg"
                    width="36px"
                    class="img-card-header"
                  />
                  <p class="section-text-bold">Twitter</p>
                </div>
                <p class="section-text">
                  Follow us <b class="text-highlight">@fissionio</b> to stay up
                  to date with Fission news.
                </p>
              </div></a
            >
          </div>
        </div>
      </div>

  <div class="col-lg-8 community-cards show-desktop">
        <div class="row">
          <div class="col-lg-6">
            <a href="/slack"
              ><div class="card-shadow">
                <div class="card-header-custom">
                  <img
                    src="/images/slack.svg"
                    width="30px"
                    class="img-card-header"
                  />
                  <p class="section-text-bold">Slack</p>
                </div>
                <p class="section-text">
                  Join the Fission community to engage with the fission team,
                  interact with other users, and contribute ideas and feedback.
                </p>
              </div></a
            >
          </div>
          <div class="col-lg-6">
            <a href="https://github.com/fission"
              ><div class="card-shadow">
                <div class="card-header-custom">
                  <img
                    src="/images/github.svg"
                    width="30px"
                    class="img-card-header"
                  />
                  <p class="section-text-bold">GitHub</p>
                </div>
                <p class="section-text">
                  Dive into the code and learn more about Fission. Submit PRs
                  and collaborate with other like-minded contributors.
                </p>
              </div></a
            >
          </div>
          <div class="col-lg-6">
            <a href="https://twitter.com/fissionio"
              ><div
                class="card-shadow twitter-card-desktop"
                style="margin-left: auto"
              >
                <div class="card-header-custom">
                  <img
                    src="/images/twitter.svg"
                    width="36px"
                    class="img-card-header"
                  />
                  <p class="section-text-bold">Twitter</p>
                </div>
                <p class="section-text">
                  Follow us <b class="text-highlight">@fissionio</b> to stay up
                  to date with Fission news.
                </p>
              </div></a
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</div>