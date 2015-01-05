---
layout: post
title: Introducing Lanyon
---

Lanyon is an unassuming [Jekyll](http://jekyllrb.com) theme that places content first by tucking away navigation in a hidden drawer. It's based on [Poole](http://getpoole.com), the Jekyll butler.

### Built on Poole

Poole is the Jekyll Butler, serving as an upstanding and effective foundation for Jekyll themes by [@mdo](https://twitter.com/mdo). Poole, and every theme built on it (like Lanyon here) includes the following:

* Complete Jekyll setup included (layouts, config, [404](/404), [RSS feed](/atom.xml), posts, and [example page](/about))
* Mobile friendly design and development
* Easily scalable text and component sizing with `rem` units in the CSS
* Support for a wide gamut of HTML elements
* Related posts (time-based, because Jekyll) below each post
* Syntax highlighting, courtesy Pygments (the Python-based code snippet highlighter)

{% highlight php %}
<?php

/*
 * This file is part of the Moss micro-framework
 *
 * (c) Michal Wachowski <wachowski.michal@gmail.com>
 *
 * For the full copyright and license information, please view the LICENSE
 * file that was distributed with this source code.
 */

namespace Moss\Container;

/**
 * Dependency Injection Component definition
 *
 * @package Moss DI Container
 * @author  Michal Wachowski <wachowski.michal@gmail.com>
 */
class Component implements ComponentInterface
{

    protected $class;
    protected $arguments;
    protected $methods;

    /**
     * Constructor
     *
     * @param string $class
     * @param array  $arguments
     * @param array  $calls
     */
    public function __construct($class, array $arguments = [], array $calls = [])
    {
        $this->class = (string) $class;

        if (!empty($arguments)) {
            $this->arguments = $arguments;
        }

        if (!empty($calls)) {
            $this->methods = $calls;
        }
    }

    /**
     * Returns component instance
     *
     * @param ContainerInterface $container
     *
     * @return object
     */
    public function __invoke(ContainerInterface $container = null)
    {
        return $this->get($container);
    }


    /**
     * Returns component instance
     *
     * @param ContainerInterface $container
     *
     * @return object
     */
    public function get(ContainerInterface $container = null)
    {
        if (empty($this->arguments)) {
            $instance = new $this->class();
        } else {
            $ref = new \ReflectionClass($this->class);
            $instance = $ref->newInstanceArgs($this->prepare($container, $this->arguments));
        }

        if (empty($this->methods)) {
            return $instance;
        }

        foreach ($this->methods as $method => $methodArguments) {
            $ref = new \ReflectionMethod($instance, $method);

            if (empty($this->arguments)) {
                $ref->invoke($instance);
            }

            $ref->invokeArgs($instance, $this->prepare($container, $methodArguments));
        }

        return $instance;
    }

    /**
     * Retrieves needed arguments from container and returns them
     *
     * @param ContainerInterface $container
     * @param array              $arguments
     *
     * @return array
     * @throws ContainerException
     */
    protected function prepare(ContainerInterface $container = null, array $arguments = [])
    {
        $result = [];

        foreach ($arguments as $k => $arg) {
            if (is_array($arg)) {
                $result[$k] = $this->prepare($container, $arg);
                continue;
            }

            if ($arg == '@Container') {
                $result[$k] = $container;
                continue;
            }

            if (strpos($arg, '@') !== 0) {
                $result[$k] = $arg;
                continue;
            }

            $arg = substr($arg, 1);

            if (!$container) {
                throw new ContainerException(sprintf('Unable to resolve dependency for "%s" - missing dependency "%s"', $this->class, $arg));
            }

            $result[$k] = $container->get($arg);
        }

        return $result;
    }
}
{% endhighlight %}

### Lanyon features

In addition to the features of Poole, Lanyon adds the following:

* Toggleable sliding sidebar (built with only CSS) via **☰** link in top corner
* Sidebar includes support for textual modules and a dynamically generated navigation with active link support
* Two orientations for content and sidebar, default (left sidebar) and [reverse](https://github.com/poole/lanyon#reverse-layout) (right sidebar), available via `<body>` classes
* [Eight optional color schemes](https://github.com/poole/lanyon#themes), available via `<body>` classes

[Head to the readme](https://github.com/poole/lanyon#readme) to learn more.

### Browser support

Lanyon is by preference a forward-thinking project. In addition to the latest versions of Chrome, Safari (mobile and desktop), and Firefox, it is only compatible with Internet Explorer 9 and above.

### Download

Lanyon is developed on and hosted with GitHub. Head to the <a href="https://github.com/poole/lanyon">GitHub repository</a> for downloads, bug reports, and features requests.

Thanks!
