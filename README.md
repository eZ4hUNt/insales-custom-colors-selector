# Кастомный селектор цвета товара для InSales
![Кастомный селектор цвета товара для InSales](https://github.com/eZ4hUNt/insales-custom-colors-selector/blob/master/Preview%20%231.png?raw=true)
1. В разделе "Свойства вариантов" создаем новое свойство, с перечнем цветов
2. Добавляем в шаблон *"product.liquid"* скрипт:
```
<script type="text/javascript">
     <!--//--><![CDATA[//><!--
    $(function() {
		var $colors = {};
        {% for option in product.options %}
           {% assign option_title = option.title | downcase %}
				   
           {% if option_title == 'цвет' %}
              {% for value in option.values %}
                 {% assign fileName = value.title %}
                 {% capture fileName %}{{fileName | replace: ' ', '_' }}.gif{% endcapture %}
                 $colors[ '{{ value.title | downcase }}' ] = '{{ fileName | file_url }}';
              {% endfor %}
           {% endif %}
   		{% endfor %}

        var
            $config = {
              selector: '#variant-select',
              product:  {{ product | json }},
              settings: {
                params:{
                  'цвет': 'color',
                  'модификация': 'select'
                },
                firstOption:true,
                color: $colors
              },
            };
        selectors = new InSales.OptionSelectors( $config );
  });
   //--><!]]>
</script>
```
где в условии *option_title == 'цвет'* указываем название свойства вариантов (Цвет), которые создали в пункте 1.
3. Подключаем шаблон модификации варианта (в *"templates.liquid"*, *"render_templates.liquid"* или другое место)
```
{% comment %}
  шаблон для блока с цветами
{% endcomment %}
<script type="text/template" hidden class="selectors" id="color">
  <% for variant in @variants : %>
    <span data-option-id="<%- variant.id %>"
          data-option-position="<%- variant.position %>"
          data-option-index="<%- @options.index %>"

          data-image-id="<%- @getImageId variant.title %>"
          data-image-original="<%- @getGalleryOriginal variant.title %>"
          data-image-preview="<%- @getGalleryPreview variant.title %>"

          class="variant-color
                variant-color--<%- @options.name %>
                <% if !variant.available : %>
                  variant-color--unavailable
                <% end %>
                <% if variant.position == @options.selected && !@options.is_disabled : %>
                  variant-color--active
                <% end %>"
    >
      <img  src="<%- @getColor variant.title %>"
            title="<%- variant.title %>"
            alt="<%- variant.title %>"
            class="variant-color_image"
      />
    </span>
  <% end %>
</script>
```
4. Заливаем в файловый менеджер картинки цвета, названия которых должны быть в формате *"<название цвета в свойствах вариантов пункта 1>.gif"*.
Например: *черный_с_серым.gif*

## Сам скрипт и пример на GitHub
https://github.com/eZ4hUNt/insales-custom-colors-selector/

Пример: http://studio-make-up.ru/product/paletka-teney-dlya-vek-5-v-1-golden-rose-professional-palette-eyeshadow-103-blue-line
