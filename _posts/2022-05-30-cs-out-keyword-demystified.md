---
layout: post
title: C# out keyword demystified
date: 2022-05-30 +13:00
published: false
category: C#
tags: [c#, out keyword, demystification]
---

https://www.reddit.com/r/csharp/comments/682tu0/out_vs_return/

Sorry for resurrecting this post, but I was bitten in the arse today by a very different implementation of out than anyone in this thread has previously touched on.
Consider the following (paying attention to namespaces).
namespace com.company.PermissionsControl
{
}

namespace com.company.PermissionsApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            List<UniquePermission> uniquePermissions = new List<UniquePermission>();
            // provision something, 
        }
    }
    // implementation elided for brevity
    private static void SetUniquePermissions(List<UniquePermission> uniquePermissions, Web web)
    {
        // set permissions using uniquePermissions
        foreach (var up in uniquePermissions)
        {
            if (string.Equals(up.permissionType, "file")
            {
                // apply the permission
            }
        }
    }
}
