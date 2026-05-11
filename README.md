> **📦 Now available on Packagist** — `composer require marcosiino/pipeflow`
>
> This repository is a fork of [marcosiino/pipeflow-php](https://github.com/marcosiino/pipeflow-php) published on Packagist as a first-party package for easier Composer installation. BSD-3-Clause license. Maintained by [@paginaviva](https://github.com/paginaviva).

---

# Pipeflow PHP

Pipeflow is a lightweight pipeline engine for PHP applications. It lets you describe complex automations as a sequence of small, reusable processing steps called stages. The real superpower is that the entire flow can be expressed in a clear XML format that is easy to read, visualise, and reason about—so even non-developers can review, maintain, and update automations without touching PHP code (but you can also configure the pipelines via hard coded php code). 

Each stage receives a shared context, performs a focused unit of work, and returns the enriched context to the next stage. By chaining stages together you can orchestrate complex jobs while keeping each piece easy to maintain and test.

In other words Pipeflow library gives you the instruments to instantiate one or more pipelines from an xml configuration, providing starting data in an initial context (optionally), and execute them when you want. What you will need to do is use these instruments in your web application to allow your actors to: edit the pipeline's configurations xml (via a text editor), save the pipeline xml configuration somewhere (e.g. your application db), and, when your application need to start a pipeline (manually or through a cron), just load the xml, feed it in the Pipeline class instance, and launch it.

## Table of Contents

- [Why Pipeflow matters](#why-pipeflow-matters)
- [Example use cases](#other-example-use-cases)
- [Installation and Documentation](#installation-and-documentation)
- [Quick introduction to pipelines](#quick-introduction-to-pipelines)
  - [Configuring with XML](#configuring-with-xml)
  - [Configuring programmatically via PHP](#configuring-programmatically-via-php)
- [Extending with custom stages](#extending-with-custom-stages)
- [Real Use Cases](#real-use-cases)
- [Learn more](#learn-more)
- [Contribute to PipeFlow](#contribute-to-pipeflow)
- [License](#license)

## Why Pipeflow matters
- **Human-friendly configuration** – describe automations in a simple XML format that even non developers can read, write and maintain.
- **Zero dependencies** – the library has zero external dependencies, making it lightweight and easy to integrate into any PHP project.
- **Reusable stages** – stages are encapsulated units of work that can be combined and reused across different pipelines.
- **Context sharing** – stages communicate through a shared context (`PipelineContext`), enabling complex data flows without tight coupling.
- **Extensible** – you can create custom stages by extending the provided abstract class, tailoring the engine to your specific needs.
- **Proven in production** – Pipeflow is being used in real world applications, including WordPress sites with AI-driven content generation.
- **No background workers** - everything runs synchronously within the same PHP process. Keep it simple.

## Other example use cases

- **E-commerce automation**: fetch products from an API, transform the data, and update your inventory.
- **Content processing pipelines**: download files, extract text, process with AI, and store results.
- **Data transformation**: decode JSON, filter arrays, and encode results in a structured pipeline.
- **API orchestration**: chain multiple API calls where the output of one becomes the input of the next.
- **Scheduled tasks**: combine Delay stage with other stages to create scheduled automation (when triggered by a cron job).
- **Multi-step forms**: guide users through a sequence of steps where each step depends on the previous one.

## Installation and Documentation

Since Pipeflow is now available on Packagist, you can install it directly:

```bash
composer require marcosiino/pipeflow
```

For full documentation, please refer to the [DOCUMENTATION.md](DOCUMENTATION.md).

## Quick introduction to pipelines

### Configuring with XML
You can configure a pipeline using a simple XML format:

```xml
<?xml version="1.0" encoding="utf-8"?>
<pipeline id="hello_example">
    <stages>
        <stage type="SetValue">
            <settings>
                <param name="parameterName">greeting</param>
                <param name="parameterValue">Hello, World!</param>
            </settings>
        </stage>
        <stage type="JSONEncode">
            <settings>
                <param name="parameterName">encoded</param>
                <param name="sourceParameterName">greeting</param>
                <param name="associativeArray">false</param>
            </settings>
        </stage>
        <stage type="Delay">
            <settings>
                <param name="ms">1000</param>
            </settings>
        </stage>
    </stages>
</pipeline>
```

Then run it in PHP:

```php
use Marcosiino\Pipeflow\Core\Pipeline;
use Marcosiino\Pipeflow\PipeFlow;

PipeFlow::registerStages();

$pipeline = new Pipeline();
$pipeline->setupWithXML($xml);
$context = $pipeline->execute();

echo $context->getParameter('encoded');
```

### Configuring programmatically via PHP
You can also build the pipeline configuration directly in PHP, without XML:

```php
use Marcosiino\Pipeflow\Core\Pipeline;
use Marcosiino\Pipeflow\PipeFlow;
use Marcosiino\Pipeflow\Core\StageConfiguration\StageConfiguration;

PipeFlow::registerStages();

$pipeline = new Pipeline();
$config = new StageConfiguration('SetValue');
$config->addSetting('parameterName', 'greeting');
$config->addSetting('parameterValue', 'Hello from PHP!');

$pipeline->addStage($config->getStage());
$context = $pipeline->execute();
```

## Extending with custom stages

Pipeflow is designed to be extensible. You can create custom stages by extending `AbstractPipelineStage` and registering them with a factory.

Check the full documentation in [`DOCUMENTATION.md`](DOCUMENTATION.md).

## Real Use Cases

Pipeflow is currently in use on the following sites, demonstrating its reliability in production environments:

- [PagineDaColorare.it](https://pagine-da-colorare.it) - A coloring pages website that uses Pipeflow to orchestrate AI-powered content generation pipelines.
- [Fiaberello.it](https://www.fiaberello.it) - A storytelling platform that leverages Pipeflow for automated fairy tale generation.

## Learn more

- [Full documentation](https://github.com/paginaviva/pipeflow-php-pack/blob/main/DOCUMENTATION.md)
- [Original repository](https://github.com/marcosiino/pipeflow-php)

## License

This project is licensed under the BSD 3-Clause License - see the [LICENSE](LICENSE) file for details.
