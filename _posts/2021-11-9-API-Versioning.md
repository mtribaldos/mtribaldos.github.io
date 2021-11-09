---
layout: post
title: API Versioning - Should I have to change an API code?
---

Software is made to be able to adapt to any circumstance, even if one can think it has reached a good degree of stability. So the question wouldn't be WHETHER as much as HOW.  The challenge becomes how best to manage these changes.

Evolving an API is not an easy path. An API stands for a "contract", that is something that both the API provider and the API consumer agree upon. The stability one would expect from these contracts may seem to contradict the ability to change, inherent in all kinds of software. So the strong duty of care to their consumers developers building and designing an API (providers) must have.

If the goal is stability, the safest option would be to deploy new versions for every change in the contract. Any API consumer interested in these changes would need to choose to use the newer version.

API consumers and providers are considered compatible when they can communicate with one another. If we, as API providers, want or need to deploy changes to our API that doesn't require any effort on the consumers, we say these changes are "**backward compatible**". In that case, the API consumers continue to function as it did before.

## How to cope with compatibility?

So, we try to build the APIs as more backward compatible as possible. The strategy is never to break anything. We would be allowed to make changes in the API provider as long as the contract is respected.

## API as Front-end service layer

A most common API use case is for serving as **a Front-end service layer** in a modular application. In this case, the front-end instances are the primary API consumers, most of the time the only, and that quickly becomes a problem: what initially was conceived of a general purpose API prepared to a broad range of use cases changes into a specialized layer for the front-end, narrowing little by little the odds that it could satisfy the needs of other API consumers.

### Example we take in Mercadona

Worse than that is the fact that the API provider **starts to depend on its API consumer**, the front-end, and this has proven to be a dangerous path. That danger is known as Coupling.

As the front-end releases new features,  its counterpart, the API provider has to evolve to support new services. Some of these changes can be made "backward compatible", some not. In the former case, there are no problems: new code gets included in the API provider, and front-ends opt in or out depending on their version, without further inconvenience. In the latter case, there is always a shortcut at hand: as front-end component includes a version tag with every release, one could cope with the differences in behaviour from past and present features spreading the code with hundreds of "if-else" statements, as a kind of misuse of *feature flags,* and "to-do" comments for remembering the fact we are in debt. The code is harder to read each time, becoming increasingly unmaintainable. 

```python
{% highlight python %}
# TODO backward compatibility front 1.2.0. To be removed
if HeadersHelper(request).is_app_version_lower_than('1.2.1'):
    for i, ordering in enumerate(pagination.ordering):
        if ordering.get('field', '').lower() == 'modification_date':
            pagination.ordering[i]['order'] = '-'
{% endhighlight %}
```

Because users have the ultimate control of when to upgrade their apps (of course I'm talking about the front-end), this chains of "if-else" should be kept in code as long as the the mass of people who employ that version is not representative. This kind of misuse of feature flags does not make it easy to track the number of users that are using distinct app versions. There should be a way to better measure what would be the most suitable moment to end the support for the old behaviours.

## Avoiding the shortcut is the solution

Yes, there is a better way of acting, in terms of readability and maintainability of code, as far as we want to keep a stable pace of development: resisting to take the aforementioned shortcut.

By just by considering the front-end as another API consumer, and not giving them privileges, this would help us to come back on to the right track. We would manage changes in our app by supporting versioning on the API provider, of course.

