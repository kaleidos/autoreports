.. contents:: 

===========
Autoreports
===========

Autoreports is a Django application that lets you create reports in very
Django Admin listing

Installation
============

In your settings.py
-------------------

::

    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.admin',

        #.....................#

        'autoreports',
    )


In your urls.py
---------------

::

    urlpatterns = patterns('',

        #...#

        (r'^autoreports/', include('autoreports.urls')),

        #...#
    )


In the admin site (from Advanced usage)
----------------------------------------

Redefine 'admin/change_list.html' templates as follows

::

  {% load adminmedia admin_list i18n autoreports_tags %} {# add auto_reports_tags #}

  #...#

  {% block object-tools %}
    {% if has_add_permission %}
      <ul class="object-tools">
        <li>
          <a href="add/{% if is_popup %}?_popup=1{% endif %}" class="addlink">
            {% blocktrans with cl.opts.verbose_name as name %}Add {{ name }}{% endblocktrans %}
          </a>
        </li>
        {% autoreports_admin %} {# add this stuff #}
      </ul>
    {% endif %}
  {% endblock %}

  #...#

By default it will takes 'list_display' fields to export in CSV.
You can redefine it in every model admin you want::

  from autoreports.admin import ReportAdmin

   #...#

  class FooModelAdmin(ReportAdmin, admin.ModelAdmin):
      pass



Basic usage
===========

You have to api, and you can export to CSV each models.

::

 /autoreports/app_label/module_name/
 /autoreports/app_label/module_name/?filter1=value1


Advanced usage
==============

You have for each change list 4 new actions:

Quick report 
------------
Report to csv the same that you see in the changelist. Works the filters and the searcher
 
Advanced report
----------------
You have a form to filter. You can customizer this form.

::

 class FooModelAdmin(ReportAdmin, admin.ModelAdmin):
    report_filter_fields = ('description', 'category', ...)
    report_display_fields = ('name', 'description',  ...)
 
If you don't define this attributes, report_filter_fields and report_display_fields have the value of list_display

Wizard report
-------------
 You can create a new "Advanced reports", with this wizard

Reports
-------
 A list of "Advanced reports" thet you created with the wizard


You can have this functionality in the public view, if you registry some Model:
To access /autoreports/.

::

 from autoreports.registry import report_registry
 from autoreports.api import ReportApi

 #...#

 class ModelApi(ReportApi):
    category = 'contrato'
    category_verbosename = 'Contrato'


 report_registry.register_api(Model, ModelApi)

Configuration
=============

You can set some settings and autoreports adapt itself:

 * AUTOREPORTS_BASE_TEMPLATE = 'base.html' # Indicate of the base template
 * AUTOREPORTS_FUNCTIONS = True  # If you want that the funcions can choose in the wizard
 * AUTOREPORTS_INITIAL = True # If you want that the advanced forms have to default the initial values
 * AUTOREPORTS_I18N = False # If you want have the forms (that you created with the wizard) in various languages
 * AUTOREPORTS_SUBFIX = True # If you want that in the filter set the filter brackets
 * AUTOREPORTS_ADAPTOR = {'datetime': 'myappreport.fields.DateTimeFieldReportField'} # If you want change some adaptor
 * AUTOREPORTS_WIZARDFIELD = 'myappreport.wizards.MyWizardField' # If you want change the WizardField


Upstream Development
====================

You can get the last bleeding edge upstream version of autoreports by doing a
checkout of its subversion repository::

  svn co http://svnpub.yaco.es/djangoapps/autoreports/trunk/autoreports
