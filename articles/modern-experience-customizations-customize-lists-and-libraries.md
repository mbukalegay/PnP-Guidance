# Customizing "modern" lists and libraries
During summer 2016 the SharePoint Online team released "modern" document libraries and lists which brings a better user experience which is faster, more intuitive and responsive. **The "modern" experiences are having many benefits and we highly recommend using them**. If your current customizations do not yet work with the "modern" experience then it's time to revisit those so your users can benefit from these great improvements:
 - "Modern" document libraries have an updated user interface that offers an experience similar to OneDrive, so it’s more intuitive to create a new folder and upload files in the browser
 - Click Pin to top to add documents “above the fold” in any onscreen view
 - Copying isn’t new, but the copy and move gestures are intelligent about displaying your information architecture and letting you create new folders on the fly
 - You may not have to make as many copies any more. Document libraries are also intelligent about remembering other files you’ve been using in SharePoint. That’s why you can import other files from other libraries as links, without having to duplicate files between multiple sites
 - The new document libraries let you group files directly in the main page without clicking to a separate admin screen. You can also click and drag to change the size of your columns, as well as sort, filter and group from any column header
 - Mobile browsers have the same features as the desktop, making SharePoint productive for every user—whether they interact via mouse, keyboard, touch or screen reader
 - You can now edit metadata directly from the main view in the information panel. No more clicking into multiple screens to apply an update!
 - Thanks to Office Online integration, you can navigate a complete document preview at the top of the information panel. The panel offers metadata, including the history of recent activity, updates to the file and who received a share to the file

This article focuses on the extensibility options there are in the "modern" library and list experiences, if you however want to learn more about the functionalities offered by the "modern" experiences then following links will help:
- Overview of the "modern" document library experience: https://blogs.office.com/2016/06/07/modern-document-libraries-in-sharepoint
- Overview of the "modern" list experience: https://blogs.office.com/2016/07/25/modern-sharepoint-lists-are-here-including-integration-with-microsoft-flow-and-powerapps 
- Differences between "modern" and "classic" experiences: https://support.office.com/en-us/article/Differences-between-classic-and-new-experiences-for-lists-and-document-libraries-30e1aab0-a5cc-4363-b7f2-09e2ae07d4dc?ui=en-US&rs=en-US&ad=US

In remainder of this article we'll use "modern" for the new user experience and "classic" for the legacy user experience. 

>**Important:** 
We're not deprecating the "classic" experience, both "classic" and "modern" will coexist.


_**Applies to:** SharePoint Online_

## Overview of the customization options
<a name="customizationoptions"> </a>

"Modern" list and libraries do not support as many customization options as "classic" lists and libraries. In this article we'll provide more details and examples on the supported options. The SharePoint team is working to support more options in the future. Below list gives a quick overview of the supported capabilities for "modern" list and libraries:
 - Subset of User Custom Actions
 - Custom branding
 - PowerApps and Flow integration

There are numerous customizations which currently are not supported for "modern" lists and libraries:
 - JSLink based field customizations - More options will become available in the future
 - JSLink based view customizations - More options will become available in the future
 - Custom CSS via AlternateCSSUrl web property
 - Custom JavaScript embedded via User Custom Actions - There will a be more controlled way to embed JavaScript on the pages through SharePoint Framework (not only client-side web parts)
 - Custom master pages - More extensive branding will be supported later using alternative options
 - Customization via InfoPath
 - Minimal Download Strategy (MDS)
 - SharePoint Server Publishing

## User Custom Actions
<a name="supportedcustomactions"> </a>

The "modern" experiences do allow certain user custom actions to be surfaced in the new user interface, but not all user action configurations which are supported by "classic" mode are supported in the "modern" experience. Below table gives a high level overview of the supported custom action locations and how they're surfaced in the "modern" UI:

|**User Custom Action location**|**Visible in "modern" UI**|
|:-----|:-----|
| `EditControlBlock` | Yes, these entries show up as custom menu items|
| `CommandUI.Ribbon` | Yes, these entries show up as toolbar items |
| All other locations (e.g. `scriptlink`) | Sorry, these user custom actions won't work |


### EditControlBlock User Custom Actions 
<a name="editcontrolblockcustomactions"> </a>

Adding custom links to the context menu can be done by using the `EditControlBlock` as location for your custom action. The below PnP provisioning XML creates 2 custom entries. 

```XML
<pnp:ProvisioningTemplate ID="EditControlBlockSamples" Version="1" xmlns:pnp="http://schemas.dev.office.com/PnP/2015/12/ProvisioningSchema">
  <pnp:CustomActions>
    <pnp:SiteCustomActions>
      <pnp:CustomAction Name="CA_1" Description="ca 1" Location="EditControlBlock" RegistrationType="List" RegistrationId="101" Title="CA 1 Title" Sequence="3000" Url="https://contoso.azurewebsites.net/pages/index.aspx" Enabled="true"/>
      <pnp:CustomAction Name="CA_2" Description="ca 2" Location="EditControlBlock" RegistrationType="List" RegistrationId="101" Title="CA 2 Title" Sequence="4000" Url="https://contoso.azurewebsites.net/pages/index.aspx" Enabled="true"/>
    </pnp:SiteCustomActions>
  </pnp:CustomActions>
</pnp:ProvisioningTemplate>
```

You can apply this [PnP provisioning template](https://msdn.microsoft.com/en-us/pnp_articles/pnp-provisioning-engine-and-the-core-library) to a site using the PnP Core library or PnP PowerShell. We've opted to show the PowerShell approach in this article. A first step is installing the PnP PowerShell module as described in https://github.com/SharePoint/PnP-PowerShell. Once that's done, save the PnP provisioning xml to a file and then 2 simple lines of PnP PowerShell are enough to apply the template:

```PowerShell

# Connect to a previously created Modern Site
$cred = Get-Credential
Connect-PnPOnline -Url https://[tenant].sharepoint.com/sites/siteurl -Credentials $cred

# Apply the PnP provisioning template
Apply-PnPProvisioningTemplate -Path c:\customaction_modern_editcontrolblock.xml -Handlers CustomActions

```

If you refresh the "modern" view of a document library in your site you'll see the new entries appear

![Custom EditControlBlock actions visible in the menu](media/modern-experiences/custom-editcontrolblock-actions.png)


>**Note**:
>If you want to use this sample for a list then please set the `RegistrationId` attribute to 100

### CommandUI.Ribbon User Custom Actions 
<a name="ribboncustomactions"> </a>

If you want to extend the toolbar in the "modern" list and library experiences you can do so via adding a user custom action targeting the CommandUI.Ribbon location as shown in the PnP provisioning XML sample.

```XML
<pnp:ProvisioningTemplate ID="CommandUIRibbonSamples" Version="1" xmlns:pnp="http://schemas.dev.office.com/PnP/2015/12/ProvisioningSchema">
  <pnp:CustomActions>
    <pnp:SiteCustomActions>
      <pnp:CustomAction Name="CA_4" Description="ca 4" Location="CommandUI.Ribbon" RegistrationType="List" RegistrationId="101" Title="CA 4 Title" Sequence="6000" Enabled="true">
        <pnp:CommandUIExtension>
          <CommandUIDefinitions>
            <CommandUIDefinition Location="Ribbon.Documents.Copies.Controls._children">
              <Button
                Id="Ribbon.Documents.Copies.OfficeDevPnPDownloadAll"
                Command="OfficeDevPnP.Cmd.DownloadAll"
                Image16by16="/_layouts/15/images/sharepointfoundation16.png"
                LabelText="Download All"
                Description="Download all files separately"
                ToolTipTitle="Download All"
                ToolTipDescription="Download all files separately"
                TemplateAlias="o1"
                Sequence="15"/>
            </CommandUIDefinition>
          </CommandUIDefinitions>
          <CommandUIHandlers>
            <CommandUIHandler
              Command="OfficeDevPnP.Cmd.DownloadAll"
              CommandAction="https://contoso.azurewebsites.net/pages/index.aspx" />
          </CommandUIHandlers>
        </pnp:CommandUIExtension>
      </pnp:CustomAction>
      <pnp:CustomAction Name="CA_6" Description="ca 6" Location="CommandUI.Ribbon" RegistrationType="List" RegistrationId="101" Title="CA 6 Title" Sequence="5000" Enabled="true">
        <pnp:CommandUIExtension>
            <CommandUIDefinitions>
              <CommandUIDefinition Location="Ribbon.CustomTabs._children">
                <Tab Id="Custom Tab" Title="Custom Tab" Description="Custom Tab">
                  <Scaling Id="Custom Tab.Scaling">
                    <MaxSize Id="Custom Group.Scaling.MaxSize" GroupId="Custom Group" Size="LargeLarge" />
                    <MaxSize Id="Custom Group 2.Scaling.MaxSize" GroupId="Custom Group 2" Size="LargeLarge" />
                    <Scale Id="Custom Group.Scaling.Scale" GroupId="Custom Group" Size="LargeLarge" />
                    <Scale Id="Custom Group 2.Scaling.Scale" GroupId="Custom Group 2" Size="LargeLarge" />
                  </Scaling>
                  <Groups Id="Custom Tab.Groups">
                    <Group Id="Custom Group 2" Title="Custom Group 2" Description="Custom Group 2" Sequence="7888" Template="Ribbon.Templates.Flexible2">
                      <Controls Id="Custom Group 2.Controls">
                        <Button Id="CustomButton3" LabelText="Custom Button 3" Image16by16="/_layouts/15/images/attach16.png" ToolTipTitle="Custom Button 3" ToolTipDescription="Custom Button 3" Command="CustomButton3.Command" TemplateAlias="o1" />
                      </Controls>
                    </Group>
                    <Group Id="Custom Group 1" Title="Custom Group 1" Description="Custom Group 1" Sequence="10000" Template="Ribbon.Templates.Flexible2">
                      <Controls Id="Custom Group 1.Controls">
                        <Button Id="CustomButton1" LabelText="Custom Button 1" Image16by16="/_layouts/15/images/itslidelibrary.png" ToolTipTitle="Custom Button 1" ToolTipDescription="Custom Button 1" Command="CustomButton1.Command" TemplateAlias="o1" />
                        <Button Id="CustomButton2" LabelText="Custom Button 2" Image16by16="/_layouts/15/images/dldsln16.png" ToolTipTitle="Custom Button 2" ToolTipDescription="Custom Button 2" Command="CustomButton2.Command" TemplateAlias="o1" />
                      </Controls>
                    </Group>
                  </Groups>
                </Tab>
              </CommandUIDefinition>
              <CommandUIDefinition Location="Ribbon.Templates._children">
                <GroupTemplate Id="Ribbon.Templates.Flexible2">
                  <Layout Title="LargeLarge" LayoutTitle="LargeLarge">
                    <OverflowSection DisplayMode="Large" TemplateAlias="o1" Type="OneRow" />
                    <OverflowSection DisplayMode="Large" TemplateAlias="o2" Type="OneRow" />
                  </Layout>
                  <Layout Title="LargeMedium" LayoutTitle="LargeMedium">
                    <OverflowSection DisplayMode="Large" TemplateAlias="o1" Type="OneRow" />
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="LargeSmall" LayoutTitle="LargeSmall">
                    <OverflowSection DisplayMode="Large" TemplateAlias="o1" Type="OneRow" />
                    <OverflowSection DisplayMode="Small" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="MediumLarge" LayoutTitle="MediumLarge">
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Large" TemplateAlias="o2" Type="OneRow" />
                  </Layout>
                  <Layout Title="MediumMedium" LayoutTitle="MediumMedium">
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="MediumSmall" LayoutTitle="MediumSmall">
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Small" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="SmallLarge" LayoutTitle="SmallLarge">
                    <OverflowSection DisplayMode="Small" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Large" TemplateAlias="o2" Type="OneRow" />
                  </Layout>
                  <Layout Title="SmallMedium" LayoutTitle="SmallMedium">
                    <OverflowSection DisplayMode="Small" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="SmallSmall" LayoutTitle="SmallSmall">
                    <OverflowSection DisplayMode="Small" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Small" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                </GroupTemplate>
              </CommandUIDefinition>
            </CommandUIDefinitions>
            <CommandUIHandlers>
              <CommandUIHandler Command="CustomButton1.Command" CommandAction="https://contoso.azurewebsites.net/pages/index.aspx" />
              <CommandUIHandler Command="CustomButton2.Command" CommandAction="http://www.bing.com" />
              <CommandUIHandler Command="CustomButton3.Command" CommandAction="http://dev.office.com/sharepoint" />
            </CommandUIHandlers>
        </pnp:CommandUIExtension>
      </pnp:CustomAction>
    </pnp:SiteCustomActions>
  </pnp:CustomActions>
</pnp:ProvisioningTemplate>
```

After adding these user custom actions you'll see them appearing in the toolbar. Notice that custom tabs are transformed into a dropdown menu:

![Custom action visible in the toolbar](media/modern-experiences/custom-actions-toolbar.png)

>**Note**:
>If you want to use this sample for a list then please set the `RegistrationId` attributes to 100 and use the below XML for the CA_4 user custom action
>
```XML
<CommandUIDefinition Location="Ribbon.Templates._children">
  <Button
    Id="Ribbon.Templates.OfficeDevPnPDownloadAll"
    Command="OfficeDevPnP.Cmd.DownloadAll"
    Image16by16="/_layouts/15/images/sharepointfoundation16.png"
    LabelText="Download All"
    Description="Download all files separately"
    ToolTipTitle="Download All"
    ToolTipDescription="Download all files separately"
    TemplateAlias="o1"
    Sequence="15"/>
</CommandUIDefinition>
```

### User Custom Action limitations 
<a name="customactionlimitations"> </a>

When developing user custom actions that need to work in modern experiences please take in account the following limitations:
 - You can't completely control the order in which the user custom actions show up: the user custom actions are added in the order the `_api/web/Lists(guid'listid')/CustomActionElements` does return the user custom actions...and this API currently does not take in account the sequence attributes. Buttons defined inside a custom tab can be ordered by adding them in the correct order in the CommandUIDefinition xml. Our sample shows Button 3 as first and that's because of the order in the XML
 - Command actions cannot contain JavaScript...using for example `CommandAction="javascript:alert('My custom Action');"` will mean the user custom action will not show up
 - Using the `ScriptLink` or `ScriptBlock` properties is not possible since they can only be used with user custom action location `ScriptLink`, which is not supported
 - The `RegistrationId` cannot refer to specific library ID (e.g. {7A46F86F-D6CC-4263-8A1B-1BC1658B506C}) or a specific content type id (e.g. 0x0101), only out of the box template types (e.g. 100 for a List or 101 for a Document Library) are allowed
 - Using image maps (e.g. `Image16by16="/_layouts/15/1033/images/formatmap16x16.png?rev=33" Image16by16Left="-144" Image16by16Top="-107"`) does not work, you'll need to specify individual images. Also note that only 16x16 images are relevant


## Custom branding
<a name="themingimpact"> </a>
If you're site happens to use a custom theme then this custom theme will be respected in the "modern" list and library experiences as shown in below sample:

![Modern list with custom branding coming from custom theme](media/modern-experiences/modern-list-with-custom-theme.png)

## How to configure the end user experience
<a name="configuremodernlibrariesandlists"> </a>
You do have multiple options to control whether the "modern" or "classic" library and list experience will be used. 

### Tenant level configuration
If you completely want to disable the "modern" experience then it's best to use the tenant setting for this. navigate to your tenant admin center (e.g. contoso-admin.sharepoint.com), go to settings and select the "classic" experience:

![SharePoint Lists and Libraries experience settings in the SharePoint Admin UI](media/modern-experiences/lists-libraries-tenant-settings.png)

### Site/Web level configuration
You can prevent a site collection or web to use the "modern" experience by enabling a feature:
- Site collection scoped feature with ID **E3540C7D-6BEA-403C-A224-1A12EAFEE4C4** for site collection control
- Web scoped feature with ID **52E14B6F-B1BB-4969-B89B-C4FAA56745EF** for web scoped control

You can use below [PnP provisioning XML](https://msdn.microsoft.com/en-us/pnp_articles/pnp-provisioning-engine-and-the-core-library) to enable this feature on site or web level

```XML
<pnp:ProvisioningTemplate ID="experiencecontrol" Version="1" xmlns:pnp="http://schemas.dev.office.com/PnP/2015/12/ProvisioningSchema">
  <pnp:Features>
    <!-- for site collection scoped control -->
    <pnp:SiteFeatures>
      <pnp:Feature ID="E3540C7D-6BEA-403C-A224-1A12EAFEE4C4" Description="Disable modern list experience"/>
    </pnp:SiteFeatures>
    <!-- for web scoped control -->
    <pnp:WebFeatures>
      <pnp:Feature ID="52E14B6F-B1BB-4969-B89B-C4FAA56745EF" Description="Disable modern list experience"/>
    </pnp:WebFeatures>
  </pnp:Features>
</pnp:ProvisioningTemplate>
```

Use following PnP PowerShell to apply this template:

```PowerShell

# Connect to a previously created Modern Site
$cred = Get-Credential
Connect-PnPOnline -Url https://[tenant].sharepoint.com/sites/siteurl -Credentials $cred

# Apply the PnP provisioning template
Apply-PnPProvisioningTemplate -Path c:\experiencecontrol.xml -Handlers Features

```

### List/Library configuration
If you want to control the experience on library level then you can use go to list settings, advanced settings and change the behavior:

![List experience configuration in the SharePoint tenant level settings in admin ui](media/modern-experiences/list-experience-setting.png)

The same can also be done using CSOM as shown in below snippet:

```C#
// Load the list you want to update
var list = context.Web.Lists.GetByTitle(title);
context.Load(list);
context.ExecuteQuery();

// Possible options are Auto (= what it's defined at tenant level), NewExperience (= "modern") and ClassicExperience
list.ListExperienceOptions = ListExperience.ClassicExperience;

// Persist the changes
list.Update();
context.ExecuteQuery();
```

>**Note**:
> - The settings at library level **override** the settings at web, site or tenant level
> - The current configuration is cached, so changes are not immediately visible

## Additional Considerations
<a name="sectionSection22"> </a>

We'll gradually introduce more customization options for "modern" list and library experiences. This introduction will be aligned with the release of additional SharePoint framework capabilities. Currently there's no exact schedule available, but note that we'll be updating the "modern" experience articles whenever new capabilities are being released.

## Additional resources
<a name="bk_addresources"> </a>

-  [Switch the default experience for lists or document libraries from new or classic](https://support.office.com/en-us/article/Switch-the-default-experience-for-lists-or-document-libraries-from-new-or-classic-66dac24b-4177-4775-bf50-3d267318caa9)
- [Overview of the "modern" document library experience](https://blogs.office.com/2016/06/07/modern-document-libraries-in-sharepoint)
- [Overview of the "modern" list experience](https://blogs.office.com/2016/07/25/modern-sharepoint-lists-are-here-including-integration-with-microsoft-flow-and-powerapps) 
- [Differences between "modern" and "classic" experiences](https://support.office.com/en-us/article/Differences-between-classic-and-new-experiences-for-lists-and-document-libraries-30e1aab0-a5cc-4363-b7f2-09e2ae07d4dc?ui=en-US&rs=en-US&ad=US)
