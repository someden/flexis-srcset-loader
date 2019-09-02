# @flexis/srcset-loader

[![NPM version][npm]][npm-url]
[![Node version][node]][node-url]
[![Peer dependencies status][peer-deps]][peer-deps-url]
[![Dependencies status][deps]][deps-url]
[![Build status][build]][build-url]
[![Dependabot badge][dependabot]][dependabot-url]

[npm]: https://img.shields.io/npm/v/@flexis/srcset-loader.svg
[npm-url]: https://npmjs.com/package/@flexis/srcset-loader

[node]: https://img.shields.io/node/v/@flexis/srcset-loader.svg
[node-url]: https://nodejs.org

[peer-deps]: https://david-dm.org/TrigenSoftware/flexis-srcset-loader/peer-status.svg
[peer-deps-url]: https://david-dm.org/TrigenSoftware/flexis-srcset-loader?type=peer

[deps]: https://david-dm.org/TrigenSoftware/flexis-srcset-loader.svg
[deps-url]: https://david-dm.org/TrigenSoftware/flexis-srcset-loader

[build]: http://img.shields.io/travis/com/TrigenSoftware/flexis-srcset-loader/master.svg
[build-url]: https://travis-ci.com/TrigenSoftware/flexis-srcset-loader

[dependabot]: https://api.dependabot.com/badges/status?host=github&repo=TrigenSoftware/flexis-srcset-loader
[dependabot-url]: https://dependabot.com/

Highly customizable loader for generating responsive images.

- [Responsive images](https://developer.mozilla.org/ru/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images) 🌠
- Optimize images with [imagemin](https://www.npmjs.com/package/imagemin) 🗜
- Convert images to [modern formats such as WebP](https://developer.mozilla.org/ru/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images#Use_modern_image_formats_boldly) 📸

## Install

```bash
npm i -D @flexis/srcset-loader
# or
yarn add -D @flexis/srcset-loader
```

## Usage

JavaScript:

```js
import src, {
    source,
    srcset,
    names
} from './image.jpg'
```

CSS:

```css
.image {
    background-image: url(./image.jpg);
}

.webp .image {
    background-image: url(./image.jpg?format=webp);
}

.image.sm {
    background-image: url(./image.jpg?width=320);
}
```

Description:

```ts
interface ISrc {
    /**
     * Image file format.
     **/
    format: 'webp' | 'jpg' | 'png' | 'gif' | 'svg';
    /**
     * Mime type of an image.
     * Example: 'image/svg+xml', 'image/jpeg'...
     **/
    type: string;
    /**
     * Postfix, computed by loader.
     * By default: `${format}${width}`
     * Example: 'jpg320', 'webp1280'...
     **/
    name: string;
    /**
     * Image width.
     **/
    width: number;
    /**
     * Image height.
     **/
    height: number;
    /**
     * Image url.
     **/
    src: string;
}

/**
 * Source image.
 */
const source: ISrc;

/**
 * All output images.
 */
const srcset: ISrc[];

/**
 * Name-to-url map.
 * Example: `{ jpg320: 'image.jpg', webp1280: 'image.webp' }`
 **/
const names: Record<string, string>;

export default source.src;
export {
    source,
    srcset,
    names
};
```

## Configuration

```js
module.exports = {
    module:  {
        rules: [{
            test: /\.jpe?g$/,
            use:  {
                loader:  '@flexis/srcset-loader',
                options: {
                    rules: [{
                        match:  '(max-width: 3000px)',
                        width:  [1, 1920, 1280, 720, 560, 320],
                        format: ['webp', 'jpg']
                    }],
                    scalingUp: false
                }
            }
        }]
    }
}
```

## Loader options

```ts
interface ICommonConfig {
    /**
     * Object with Sharp configs for each supported format.
     */
    processing?: Partial<IProcessingConfig>;
    /**
     * Object with imagemin plugins for each format.
     */
    optimization?: Partial<IOptimizationConfig>;
    /**
     * Do not optimize output images.
     */
    skipOptimization?: boolean;
    /**
     * Generate images with higher resolution than they's sources are.
     */
    scalingUp?: boolean;
    /**
     * Postfix string or function to generate postfix for image.
     */
    postfix?: Postfix;
}

interface IRule extends ICommonConfig {
    /**
     * There is support of 3 types of matchers:
     * 1. Glob pattern of file path;
     * 2. Media query to match image by size;
     * 3. `(path: string, size: ISize, source: Vinyl) => boolean` function.
     */
    match?: Matcher;
    /**
     * Output image(s) formats to convert.
     */
    format?: SupportedExtension|SupportedExtension[];
    /**
     * Output image(s) widths to resize, value less than or equal to 1 will be detected as multiplier.
     */
    width?: number|number[];
}

/**
 * https://github.com/webpack-contrib/file-loader
 */
interface ILoaderOptions {
    /**
     * https://github.com/webpack-contrib/file-loader#name
     */
    name: string|Function;
    /**
     * https://github.com/webpack-contrib/file-loader#outputpath
     */
    outputPath: string|Function;
    /**
     * https://github.com/webpack-contrib/file-loader#publicpath
     */
    publicPath: string|Function;
    /**
     * https://github.com/webpack-contrib/file-loader#context
     */
    context: string;
    /**
     * https://github.com/webpack-contrib/file-loader#emitfile
     */
    emitFile: boolean;
}

/**
 * Options:
 */
interface IConfig extends ICommonConfig, ILoaderOptions {
    /**
     * Rules.
     */
    rules?: IRule[];
    /**
     * Default exported image description.
     * Also you can pass it through query parameters.
     * Example: `background-image: url(./image.jpg?width=320&format=webp);`
     */
    exports?: {
        width?: number;
        format?: 'webp' | 'jpg' | 'png' | 'gif' | 'svg',
        /**
         * Use commonjs export instead of ES6 exports.
         * Notice: Vue doesn't support ES6 exports with loaders, so you should set this prop to `true`.
         */
        default?: boolean;
    };
}
```

- [`IProcessingConfig`](https://trigensoftware.github.io/flexis-srcset/interfaces/_types_.iprocessingconfig.html)
- [`IOptimizationConfig`](https://trigensoftware.github.io/flexis-srcset/interfaces/_types_.ioptimizationconfig.html)
- [`Postfix`](https://trigensoftware.github.io/flexis-srcset/modules/_types_.html#postfix)
- [`Matcher`](https://trigensoftware.github.io/flexis-srcset/modules/_helpers_.html#matcher)
- [`SupportedExtension`](https://trigensoftware.github.io/flexis-srcset/modules/_extensions_.html#supportedextension)

## Using with TypeScript

Add it to your `globals.d.ts`:

```ts
declare module '*.jpg' {
    const src: import('@flexis/srcset-loader/types').Src;
    const source: import('@flexis/srcset-loader/types').Source;
    const srcset: import('@flexis/srcset-loader/types').Srcset;
    const names: import('@flexis/srcset-loader/types').Names;
    export default src;
    export {
        source,
        srcset,
        names
    };
}
```
