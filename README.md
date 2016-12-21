# Construyendo una styleguide con React (2016-12-21)

Speakers: [@vctrfrnndz](https://twitter.com/vctrfrnndz) y [@arielger_](https://twitter.com/arielger_)

Slides: http://slides.com/arielgerstein/construyendo-una-styleguide-con-react
Auth0 Styleguide: http://styleguide.auth0.com/ - github.com/auth0/styleguide
Auth0 React Styleguide: https://auth0-styleguide-pr-102.herokuapp.com - https://github.com/auth0/styleguide/tree/experimental

La charla se va a dividir en tres partes:

1. ¿Cuáles son las ventajas de construir una styleguide?
2. ¿Cómo podemos comenzar a crear nuestra styleguide?
3. ¿Cómo evolucionamos nuestra styleguide en Auth0?

## ¿Cuáles son las ventajas de construir una styleguide?

Una styleguide es un conjunto de patrones reutilizables que nos permite:
- Mantener la consistencia y la identidad de nuestra organización.
- Promover mejores practicas.
- Simplificar el trabajo de los desarrolladores.

## ¿Cómo podemos comenzar a crear nuestra propia styleguide?

- Documentación de fuentes, variables, colores, clases de utilidad y sus distintos usos.
- Agregar los componentes basicos que se reutilizan en todos las paginas (Header, footer, hero, banners).
- Asignar mantainers al proyecto (la styleguide debe ser parte del workflow de la organización).
- Hacerla publica.
- Aumentar el alcance (agregar brand assets, design language, voice and tone, animations, etc).
- Incluir la styleguide en todos nuestros proyectos.

Pueden encontrar mas información en: http://styleguides.io/

## ¿Como evolucionamos nuestra styleguide en Auth0? 📈
### Ventajas de usar React para nuestra styleguide:

- Ecosistema pujante con variedad de componentes, tutoriales, información, etc.

- Nos permite abstraernos del mark-up de nuestros componentes (HTML/CSS). Nos enfocamos en la API externa del componente mientras que podemos cambiar el HTML/CSS interno.

  Con nuestros componentes HTML/CSS nos vemos obligados a mantener el código indefinidamente:

  > Whilst this is an excellent goal, the problem in this case is the distribution of templates.
  Even if we presume that the rendered markup is absolutely up to date, once they copy that
  code they are essentially cutting a version which needs to be maintained indefinitely. 
  When they copied the markup for a working component it had an implicit link to a snapshot of
  the CSS at that point. If you then update the template or refactor the CSS, you need to
  update all versions of the template scattered around your site.

  [A Maintainable Style Guide](http://pda.postach.io/post/a-maintainable-style-guide-ian-feather)

- Flexibilidad: Podemos combinar nuestros componentes de distintas maneras.

### Decisiones a la hora de crear una libreria de componentes React: 🤔

- ¿Cómo manejar los estilos de cada componente? 🖌
  - CSS plano (con naming conventions: [BEM](https://en.bem.info/methodology/quick-start/), [SMACSS](https://smacss.com/), etc)
  - CSS Modules (estilos con scope local)
  - Otras soluciones de [css-in-js](https://github.com/MicheleBertoli/css-in-js) ([aphrodite](https://github.com/Khan/aphrodite), [styled-components](https://github.com/styled-components/styled-components), [radium](https://github.com/FormidableLabs/radium), [styled-jsx](https://github.com/zeit/styled-jsx), etc)

- ¿Cómo dividir nuestra styleguide en secciones? 📦

  Utilizamos una herramienta llamada [Lerna](https://lernajs.io/) que nos permite manejar repositorios con distintos paquetes.
  Lerna nos permite linkear los proyectos entre si (`lerna bootstrap`) y manejar el versionado de cada proyecto (independientemente o conjuntamente).

- Testing ✅

  Actualmente usamos Mocha, Chai y [Enzime](https://github.com/airbnb/enzyme) para testear nuestros componentes.

  ```
  import React from 'react';
  import { expect } from 'chai';
  import { shallow } from 'enzyme';
  import Select from './';

  const options = [
    'Gernot Linas',
    'Butrus Aziz',
    'Vladimir Gislin',
    'Kassandros Ekwueme',
    'Rashad Ariel'
  ];

  describe('<Select />', () => {
    it('it should render a select element', () => {
      const wrapper = shallow(<Select />);
      expect(wrapper.find('select')).to.have.length(1);
    });
    it(`should render ${options.length} options`, () => {
      const wrapper = shallow(<Select options={options} />);
      expect(wrapper.find('select').find('option')).to.have.length(options.length);
    });
  });
  ```

- Generamos distintos bundles (ES6 modules, CommonJS, UMD). 
  Si usamos un module bundler como Rollup obtenemos:
  - Tree-shaking: Excluye imports no utilizados. ([example](http://rollupjs.org/?version=0.37.2&shareable=JTdCJTIyb3B0aW9ucyUyMiUzQSU3QiUyMmZvcm1hdCUyMiUzQSUyMmVzJTIyJTJDJTIybW9kdWxlTmFtZSUyMiUzQSUyMm15QnVuZGxlJTIyJTJDJTIyZ2xvYmFscyUyMiUzQSU3QiU3RCUyQyUyMm1vZHVsZUlkJTIyJTNBJTIyJTIyJTdEJTJDJTIybW9kdWxlcyUyMiUzQSU1QiU3QiUyMm5hbWUlMjIlM0ElMjJtYWluLmpzJTIyJTJDJTIyY29kZSUyMiUzQSUyMmltcG9ydCUyMCU3QiUyMGN1YmUlMjAlN0QlMjBmcm9tJTIwJy4lMkZtYXRocy5qcyclM0IlNUNuY29uc29sZS5sb2coJTIwY3ViZSglMjA1JTIwKSUyMCklM0IlMjAlMkYlMkYlMjAxMjUlMjIlN0QlMkMlN0IlMjJuYW1lJTIyJTNBJTIybWF0aHMuanMlMjIlMkMlMjJjb2RlJTIyJTNBJTIyJTJGJTJGJTIwVGhpcyUyMGZ1bmN0aW9uJTIwaXNuJ3QlMjB1c2VkJTIwYW55d2hlcmUlMkMlMjBzbyU1Q24lMkYlMkYlMjBSb2xsdXAlMjBleGNsdWRlcyUyMGl0JTIwZnJvbSUyMHRoZSUyMGJ1bmRsZS4uLiU1Q25leHBvcnQlMjBmdW5jdGlvbiUyMHNxdWFyZSUyMCglMjB4JTIwKSUyMCU3QiU1Q24lNUN0cmV0dXJuJTIweCUyMColMjB4JTNCJTVDbiU3RCU1Q24lNUNuJTJGJTJGJTIwVGhpcyUyMGZ1bmN0aW9uJTIwZ2V0cyUyMGluY2x1ZGVkJTVDbmV4cG9ydCUyMGZ1bmN0aW9uJTIwY3ViZSUyMCglMjB4JTIwKSUyMCU3QiU1Q24lNUN0JTJGJTJGJTIwcmV3cml0ZSUyMHRoaXMlMjBhcyUyMCU2MHNxdWFyZSglMjB4JTIwKSUyMColMjB4JTYwJTVDbiU1Q3QlMkYlMkYlMjBhbmQlMjBzZWUlMjB3aGF0JTIwaGFwcGVucyElNUNuJTVDdHJldHVybiUyMHglMjAqJTIweCUyMColMjB4JTNCJTVDbiU3RCUyMiU3RCU1RCU3RA==))
  - Inlining: Todos los modulos estan en el mismo scope. (Webpack 1 pone cada modulo en un scope distinto)

- Para el ambiente de desarrollo de los componentes usamos [Storybook](https://getstorybook.io/),
  que nos permite desarrollar nuestros componentes de manera aislada y mostrar distintos estados de los mismos.

  ![StoryBook](https://getstorybook.io/static/media/demo.f13d28a7.gif "StoryBook")

  ```
  import React from 'react';
  import { storiesOf, action } from '@kadira/storybook';

  storiesOf('Button', module)
    .add('with text', () => (
      <button onClick={action('clicked')}>Hello Button</button>
    ))
    .add('with some emoji', () => (
      <button onClick={action('clicked')}>😀 😎 👍 💯</button>
    ));
  ```

- Usamos [React docgen](https://github.com/reactjs/react-docgen) para generar documentacion en base a los componentes.
  React docgen parsea el archivo de nuestro componente y nos devuelve un JSON con informacion sobre las `props`, `defaultProps`, `propTypes`.
  Incluso podemos agregar comentarios a modo de descripcion de las propiedades del componente.

  ```
  var React = require('react');

  /**
  * General component description.
  */
  var Component = React.createClass({
    propTypes: {
      /**
      * Description of prop "foo".
      */
      foo: React.PropTypes.number,
      /**
      * Description of prop "bar".
      */
      bar: React.PropTypes.string
    },

    render: function() {
      // ...
    }
  });

  module.exports = Component;
  ```

- Live playground para probar los componentes:
  - Comenzamos usando [component-playground](https://github.com/FormidableLabs/component-playground)
  pero es muy pesado ya que requiere compilación de Babel en tiempo real (mas info: https://github.com/FormidableLabs/component-playground/issues/59)
  - Actualmente usamos [Codepen](http://codepen.io/) (nos permite hacer un POST y configurar el pen para que tenga el codigo de ejemplo https://blog.codepen.io/documentation/api/prefill/).

- Agregar reglas para los nuevos componentes asi nos aseguramos que nuestra libreria sea consistente. 📝

  - Agregar una configuracion de ESLint para React (https://github.com/yannickcr/eslint-plugin-react)
  - Contiene unicamente stateless componentes lo cual nos permite conectar los componentes con Redux u otras librerias de state-management.
  - Podemos re-exportar componentes de otras librerias desde nuestra styleguide.
