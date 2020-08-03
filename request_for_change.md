# Request for Change

Any service that is 'live' in Defra requires submitting a Request for Change (RfC) before changing anything in production.

This applies to _all_ changes. Even something as small as changing the value of an environment variable requires an RfC!

Submit your RfC in [myIT](https://defra.service-now.com) (also known as **Service Now**). You'll need to be on the corporate network to access **myIT**. For those without a corporate laptop access from your phone is the only option 😞.

## Time frames

There are 3 types of RfC. The key differentiators are

- the lead time before you can apply change
- the approval process they goes through

Expedited and emergency changes are used only as a last resort.

### Normal

Have a 10 working day lead time. Approvals are sought by email by the Change management team before approving the RfC.

### Expedited

For changes that need implementing in 4 to 10 days. The next Change Approval Board (CAB) will discuss the RfC . Someone from the team will need to attend to answer any questions before they grant approval.

### Emergency

For changes that need implementing in 1 to 3 days. An emergency CAB will convene to discuss the RfC. Someone from the team will need to attend to answer any questions before they grant approval.

These are really discouraged by Change management and you _will_ be challenged on why it is needed.

The following covers each field and/or section you'll need to complete. We've used an update to the apps as an example. But you will need to amend the details depending on the nature of the change.

## myIT form

Below the sub-headings refer to fields. Quoted content represents an example of what to enter. If you are unsure refer to previous RfC's for a service. By now there are plenty!

### Top section

Most are drop-downs. Anything not mentioned will either have a default or update based on what you enter.

#### Department

> Defra

#### Category

> Software

#### Environment

> Production

#### Configuration item

> Waste Carriers, Dealers, Brokers

#### Sponsor

> Victoria Titchener

#### Short description

> Deploy service changes - Waste Carriers

#### Description

> We need to update the applications that make up the service, as the update includes improvements and fixes to the 'edit a registration' function for internal users
>
> Waste Carriers Back Office
> https://github.com/DEFRA/waste-carriers-back-office/blob/master/CHANGELOG.md
> v1.8.0
> - Enable Edit registration feature #805 (Cruikshanks)
> - Add api endpoint for acceptance tests #800 (cintamani)
> - Fix json dependency security issue #807 (Cruikshanks)
> - Bump github_changelog_generator from 1.15.1 to 1.15.2 #802 (dependabot-preview[bot])
> - Bump database_cleaner from 1.8.3 to 1.8.4 #798 (dependabot-preview[bot])
> - Bump github_changelog_generator from 1.15.0 to 1.15.1 #797 (dependabot-preview[bot])
>
> Waste Carriers Front Office
> https://github.com/DEFRA/waste-carriers-front-office/blob/master/CHANGELOG.md
> v1.3.3
> - Remove Edit link from dashboard search results #482 (Cruikshanks)
> - Fix json dependency security issue #484 (Cruikshanks)
> - Bump github_changelog_generator from 1.15.1 to 1.15.2 #478 (dependabot-preview[bot])
> - Bump github_changelog_generator from 1.15.0 to 1.15.1 #476 (dependabot-preview[bot])
> - Bump database_cleaner from 1.8.3 to 1.8.4 #475 (dependabot-preview[bot])
>
> Waste Carriers Frontend
> https://github.com/DEFRA/waste-carriers-frontend/blob/master/CHANGELOG.md
> v2.6.4
> - Update edit link to go to back office #312 (irisfaraway)
> - Fix agency link to Edit registration #315 (Cruikshanks)
> - Bump github_changelog_generator from 1.15.1 to 1.15.2 #314 (dependabot-preview[bot])
> - Bump github_changelog_generator from 1.15.0 to 1.15.1 #313 (dependabot-preview[bot])
> - Update VCR cassettes #311 (Cruikshanks)
> - Bump pry-byebug from 3.8.0 to 3.9.0 #310 (dependabot-preview[bot])
> - Bump wicked_pdf from 2.0.1 to 2.0.2 #309 (dependabot-preview[bot])
> - Bump webmock from 3.8.2 to 3.8.3 #308 (dependabot-preview[bot])

### Planning

The next section of the form has 6 tabs. The only ones we care about are 'Planning' and 'Schedule'.

#### Business justification

> The Ruby Services team is working on improving existing functionality by migrating it from the original application to the new back-office application. This work is critical to improve
>
> - maintenance of the service
> - test coverage
> - the time it takes to make further changes and improvements
> - reduce bugs and unexpected behaviour

#### Implementation plan

> Execute the automated deployment job from Jenkins for each of the applications to be updated.

#### Downtime

> No

#### Risk and impact analysis

> The automated deployments will automatically rollback any changes made should any stage fail. When it has completed we will then confirm the service is still up and unaffected.

#### Blackout plan

> The automated deployments have a rollback option, which allows them to revert to the previous version of the applications, which are cached on the server.

#### Test plan

> The automated deployments are the same process in all our environments and so are run numerous times each day. The specific versions of the applications being deployed have been tested in pre-production.

### Schedule

Avoid Friday's if you can. Even if the change is small and low risk no one likes to tempt fate before the weekend!

Check the calendars of the web-ops team before hand as well. As long as

- there are no other RfC's that AM/PM
- the change is something they've done before

You should be fine to go ahead and schedule it. If unsure contact web-ops first to agree a date and time.

#### Planned start date

> 07/05/2020 09:30:00

#### Planned end date

> 07/05/2020 10:00:00

### Risk assessment

This is a link in the form. Often it does not appear until you have completed the other sections and clicked the `Save for later` button. Once you have completed this you can then click `Validate`. This is what actually submits the RfC (assuming its valid).

#### Does the change impact Application, Infrastructure or both?

> Applications

#### Is this change impacting a Datacentre?

> No

#### How many systems are impacted?

> 1-3

#### How many services are impacted?

> 1-3

#### At what level will the service be affected

> None

#### Is new infrastructure hardware or application software being introduced by this change?

> No

#### Are there multiple parties involved in the implementation of this change?

> 1 person

#### Is these a single coordinator for the change?

> Yes

#### Are there any other critical changes dependent on this implementation?

> N/A

#### Change frequency

> Change is actioned on a regular basis

#### Does this change have a high VIP visibility?

> No

#### How many users are affected?

> Internal & External customers

#### Select service classification

> N/A
